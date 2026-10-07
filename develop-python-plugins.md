Python is a common language that allows rapid prototyping without compilation.

## About Flow's Python plugins

Python plugins use the [JSON-RPC](https://flow-launcher.github.io/docs/#/json-rpc) protocol to communicate with Flow through structured JSON calls.

When building a Python plugin, keep the following in mind:

* Most importantly, users should not have to install the dependencies in requirements.txt manually; the experience should be seamless. To achieve this, add the following three things to your project:
    1. Add a GitHub workflow — a workflow that installs all your plugin's dependencies, including the Python flowlauncher module, into a folder called Lib inside your plugin.
    2. Publish all as a zip — zip up your project, including the lib directory with the modules, and publish it on the GitHub Releases page.
    3. Point your module imports to the lib directory — add the lib directory to the module search path before the first import.

* Users can use their own system-installed Python with Flow Launcher, but most use Flow Launcher's download of [Embedded Python](https://docs.python.org/3/using/windows.html#the-embeddable-package). This download is isolated from the user's system and does not prepend the script's run directory to `sys.path`.<sup>[ref](https://bugs.python.org/issue28245)</sup> To import external files, follow the example below.

* External libraries that include compiled code can cause compatibility issues across Python versions, because the compiled code is platform-specific and tied to a specific Python version. If you *must* use an external library with compiled code, consider alternative packaging methods such as [nuitka](http://nuitka.net/), or [pyinstaller](https://pyinstaller.org/en/stable/).

### Simple Example
Look at this simple [example plugin](https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldPython). It has a folder called `.github/workflows` with a file called 'Publish Release.yml', the workflow file GitHub uses to run the project's CI/CD.

[main.py](https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldPython/blob/main/main.py), in the repo's root folder, is the plugin's entry file. Notice it has this code block:
```python
import sys
from pathlib import Path

plugindir = Path.absolute(Path(__file__).parent)
paths = (".", "lib", "plugin")
sys.path = [str(plugindir / p) for p in paths] + sys.path
```

With the `lib` folder on `sys.path`, external libraries can be imported:
```python
from flowlauncher import FlowLauncher #external library
import webbrowser #Not external
```

The plugin class inherits from the FlowLauncher class provided by the FlowLauncher library. This lets the plugin communicate with Flow Launcher.

```python
class HelloWorld(FlowLauncher):
```

When a user activates the plugin, Flow Launcher calls its `query` method, passing the user's text as the `query` argument.

To respond, return a list of dictionaries as shown below. The `JsonRPCAction` dict names a method for Flow Launcher to call, with the parameters you provide. This method *must* be part of your plugin class.

```python
    def query(self, query):
        return [
            {
                "Title": "Hello World, this is where title goes. {}".format(('Your query is: ' + query , query)[query == '']),
                "SubTitle": "This is where your subtitle goes, press enter to open Flow's url",
                "IcoPath": "Images/app.png",
                "ContextData": ["foo", "bar"],
                "JsonRPCAction": {
                    "method": "open_url",
                    "parameters": ["https://github.com/Flow-Launcher/Flow.Launcher"]
                }
            }
        ]
```

Flow calls this method when a user selects the result:

```python
    def open_url(self, url):
        webbrowser.open(url)
```

The user opens the context menu with <kbd>Shift</kbd>+<kbd>Enter</kbd> or by right-clicking a result. The `context_menu` method works like `query`, but instead of a `query` argument it receives a `data` argument with the `ContextData` of the selected result.

```python
    def context_menu(self, data):
        return [
            {
                "Title": "Hello World Python's Context menu",
                "SubTitle": "Press enter to open Flow the plugin's repo in GitHub",
                "IcoPath": "Images/app.png",
                "JsonRPCAction": {
                    "method": "open_url",
                    "parameters": ["https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldPython"]
                }
            }
        ]
```


## Project setup

### 1. Add GitHub workflow
The workflow [file](https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldPython/blob/main/.github/workflows/Publish%20Release.yml) builds and deploys your project. It does the following:
1. `workflow_dispatch:` lets you run the workflow manually from your project's Actions section

2. It runs on every push to main, except pushes that only change the workflow file.

```yml
push:
    branches: [ main ]
    paths-ignore: 
      - .github/workflows/*
```

3. It specifies the Python version used to build your project:

```yml
    env:
      python_ver: 3.11
```

4. The CI reads the release version from your plugin.json and uses it to tag the release:

```yml
- name: get version
  id: version
  uses: notiz-dev/github-action-json-property@release
  with: 
    path: 'plugin.json'
    prop_path: 'Version'
```

5. The **Install dependencies** section does most of the CI work. It installs requirements.txt into the `./lib` folder (the `-t` parameter), then zips the lib folder up along with your project using `zip -r Flow.Launcher.Plugin.HelloWorldPython.zip . -x '*.git*'`. Replace `Flow.Launcher.Plugin.HelloWorldPython` with the name of your plugin.
    
    You can also add steps here to unpack or install other dependencies your plugin requires, for example compiling translation files like [this one in the Currency plugin](https://github.com/deefrawley/Flow.Launcher.Plugin.Currency/blob/23770ee929af059b1b1b7f9b5f3327b692ac9587/.github/workflows/Publish%20Release.yml#L34)

```yml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r ./requirements.txt -t ./lib
    zip -r Flow.Launcher.Plugin.HelloWorldPython.zip . -x '*.git*'
```

### 2. Publish as zip
The final **Publish** section uploads the zip file to the GitHub Releases page, tagged with the version read from your plugin.json in the earlier step. Again, replace `Flow.Launcher.Plugin.HelloWorldPython` with the name of your plugin.
```yml
- name: Publish
  if: success()
  uses: softprops/action-gh-release@v1
  with:
    files: 'Flow.Launcher.Plugin.HelloWorldPython.zip'
    tag_name: "v${{steps.version.outputs.prop}}"
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

This [blog post](https://blog.ipswitch.com/how-to-build-your-first-github-actions-workflow) gives a simple explanation of GitHub Actions workflows.

### 3. Use lib directory
With the lib folder included in your zip release, users don't need to run pip install. At runtime, tell Python to find the modules in your local lib folder, using exactly this code block:
```python
import sys
from pathlib import Path

plugindir = Path.absolute(Path(__file__).parent)
paths = (".", "lib", "plugin")
sys.path = [str(plugindir / p) for p in paths] + sys.path

```
Add this code at the top of your init file, usually [main.py](https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldPython/blob/main/main.py). It adds the paths of your lib and plugin directories on the user's machine to `sys.path`, the sys module's list of directories the Python interpreter searches for modules. If a module isn't among the interpreter's built-in modules, it then looks in those directories, including the 'lib' folder where your GitHub workflow installed the modules.

## Write the code

### 1. Start with a branch
The CI from the [previous step](/develop-python-plugins.md?id=project-setup) creates a release whenever you push or merge to the 'main' branch. So work on your plugin in a separate git branch, where commits and pushes don't create a new release each time.

It's good practice to create a branch for each new feature or fix; if you're not sure how, follow this [video tutorial](https://www.gitkraken.com/learn/git/problems/create-git-branch). When you've finished, merge the branch into 'main', which creates a new release with the version from your `plugin.json`.

### 2. main.py
Your `main.py` should look something like this:

```python
import sys,os
parent_folder_path = os.path.abspath(os.path.dirname(__file__))
sys.path.append(parent_folder_path)
sys.path.append(os.path.join(parent_folder_path, 'lib'))
sys.path.append(os.path.join(parent_folder_path, 'plugin'))

from flowlauncher import FlowLauncher
import webbrowser


class HelloWorld(FlowLauncher):

    def query(self, query):
        return [
            {
                "Title": "Hello World, this is where title goes. {}".format(('Your query is: ' + query , query)[query == '']),
                "SubTitle": "This is where your subtitle goes, press enter to open Flow's url",
                "IcoPath": "Images/app.png",
                "JsonRPCAction": {
                    "method": "open_url",
                    "parameters": ["https://github.com/Flow-Launcher/Flow.Launcher"]
                },
                "Score": 0
            }
        ]

    def context_menu(self, data):
        return [
            {
                "Title": "Hello World Python's Context menu",
                "SubTitle": "Press enter to open Flow the plugin's repo in GitHub",
                "IcoPath": "Images/app.png", # related path to the image
                "JsonRPCAction": {
                    "method": "open_url",
                    "parameters": ["https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldPython"]
                },
                "Score" : 0
            }
        ]

    def open_url(self, url):
        webbrowser.open(url)

if __name__ == "__main__":
    HelloWorld()

```

<br>

### 3. Query entry point 
`def query(self, query):`

This is the main entry point to your plugin. It returns a list of results, which can contain one or many results.

### 4. Assigning an action to your results  
`JsonRPCAction`

This specifies the method to run when the user selects the result.
In this example, selecting the result calls the `open_url` method with a URL that opens the Flow Launcher GitHub repo.

### 5. Create an additional context menu
`def context_menu(self, data):`

This method creates a context menu for your results, which the user opens with `Shift + Enter` to carry out additional tasks. Use it for tasks specific to your results. For example, the Explorer plugin returns file results, and a file's context menu lets users copy the file.

To attach a method to a context menu result, define a JsonRPCAction with the method and parameters, as for normal results. Here, the context menu opens the HelloWorldPython plugin's GitHub repo.

### 6. Result score
The `score` field assigns a weight to a result: the higher the score, the higher the result appears in Flow's result list. Scores are usually between 0 and 100. Keep it at 0 if your plugin is usually triggered by an action keyword; with a global action keyword (`*`), the average weight is 50. Users can also adjust the score in Flow's plugin settings. Flow's own fuzzy search scores range from 0 to 100, so plugins using the global action keyword should stay in that range to blend in with other results. Flow also raises the score of results the user has selected before, matching them by `Title` and `SubTitle`, so keep those consistent between queries.

### 7. Your plugin.json

If you haven't already, create a plugin.json file, which tells Flow how to load your plugin.

Place it in the top-level folder.

For what to include in your plugin.json, see the [plugin.json reference](/plugin.json.md).

## Release your plugin to Flow's Plugin Store 

To release your plugin, follow the instructions in Flow's [plugin repo](https://github.com/Flow-Launcher/Flow.Launcher.PluginsManifest).

## Good references to follow

Here are some plugins to use as a reference:
- IsPrime https://github.com/lvonkacsoh/Flow.Launcher.Plugin.IsPrime
- RollDice https://github.com/lvonkacsoh/Flow.Launcher.RollDice
- FancyEmoji https://github.com/Ma-ve/Flow.Launcher.Plugin.FancyEmoji
- Steam Search https://github.com/Garulf/Steam-Search
- Currency Converter https://github.com/deefrawley/Flow.Launcher.Plugin.Currency
