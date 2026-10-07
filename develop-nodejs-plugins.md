JavaScript, the language of the web, can be used to write Flow plugins.

## About Flow's TypeScript/JavaScript plugins

Plugins written in TypeScript/JavaScript use the [JSON-RPC](https://flow-launcher.github.io/docs/#/json-rpc) protocol to communicate with Flow through structured JSON calls.

Although not a hard requirement, this guide uses Node.js to run the TypeScript/JavaScript, and calls TypeScript/JavaScript plugins Node.js plugins from here on.

When building a Node.js plugin, keep the following in mind:

* Most importantly, users should not have to install dependencies with npm manually; the experience should be seamless. To achieve this, add the following three things to your project:
    1. Add a GitHub workflow — a workflow that installs all your plugin's dependencies into a folder called `node_modules`.
    2. Publish all as a zip — zip up your project, including the node_modules directory with the modules, and publish it on the GitHub Releases page.
    3. Point your module path to the node_modules directory — load all modules from that directory.

* Users can use their system-installed Node.js with Flow Launcher, but most use Flow Launcher's download of [Node.js](https://nodejs.org/dist/v16.18.0/node-v16.18.0-win-x64.zip). This portable Node.js is isolated from the user's system and can simply be removed.

### Simple Example
Look at this simple [example plugin](https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldNodeJS). It has a folder called `.github/workflows` with a file called `Publish Release.yml`, the workflow file GitHub uses to run the project's CI/CD. [main.js](https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldNodeJS/blob/main/main.js), in the repo's root folder, is the plugin's entry file.

## Add GitHub workflow
The workflow [file](https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldNodeJS/blob/main/.github/workflows/Publish%20Release.yml) builds and deploys your project. It does the following:
1. `workflow_dispatch:` lets you run the workflow manually from your project's Actions section

2. It runs on every push to main, except pushes that only change the workflow file.

```yml
push:
    branches: [ main ]
    paths-ignore: 
      - .github/workflows/*
```

3. It specifies the Node.js version used to build your project:

```yml
- name: Set up Node.Js
  uses: actions/setup-node@v2
  with:
    node-version: '17.3.0'
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

5. The **Install dependencies** section does most of the CI work. It runs `npm install`, which installs the dependencies listed in package.json into the 'node_modules' directory. The workflow then zips them up along with your project using `zip -r Flow.Launcher.Plugin.HelloWorldNodeJS.zip . -x '*.git*'`. Replace `Flow.Launcher.Plugin.HelloWorldNodeJS` with the name of your plugin.

```yml
- name: Install dependencies
  run: |
    npm install
    zip -r Flow.Launcher.Plugin.HelloWorldNodeJS.zip . -x '*.git*'
```

### Publish as zip
The final **Publish** section uploads the zip file to the GitHub Releases page, tagged with the version read from your plugin.json in the earlier step. Again, replace `Flow.Launcher.Plugin.HelloWorldNodeJS` with the name of your plugin.

```yml
- name: Publish
  uses: softprops/action-gh-release@v1
  with:
    files: 'Flow.Launcher.Plugin.HelloWorldNodeJS.zip'
    tag_name: "v${{steps.version.outputs.prop}}"
  env:
    GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

This [blog post](https://blog.ipswitch.com/how-to-build-your-first-github-actions-workflow) gives a simple explanation of GitHub Actions workflows.

### Use node_modules directory
With the `node_modules` folder included in your zip release, users don't need to run npm install for the plugin's dependencies. At runtime, tell the plugin to find the modules in your local node_modules directory, using exactly this code block in your [main.js](https://github.com/Flow-Launcher/Flow.Launcher.Plugin.HelloWorldNodeJS/blob/main/main.js):
```javascript
const open = require('./node_modules/open');
```

## Start with a branch
The CI from the [previous step](/develop-nodejs-plugins.md?id=add-github-workflow) creates a release whenever you push or merge to the 'main' branch. So work on your plugin in a separate git branch, where commits and pushes don't create a new release each time.

It's good practice to create a branch for each new feature or fix; if you're not sure how, follow this [video tutorial](https://www.gitkraken.com/learn/git/problems/create-git-branch). When you've finished, merge the branch into 'main', which creates a new release with the version from your `plugin.json`.

### main.js
Your main.js should look something like this:
```js
const open = require('./node_modules/open');

const { method, parameters, settings } = JSON.parse(process.argv[2]);

if (method === "query") {
	console.log(JSON.stringify(
		{
			"result": [{
				"Title": "Hello World Typescript",
				"SubTitle": "Showing your query parameters: " + parameters + ". Click to open Flow's website",
				"JsonRPCAction": {
                    "method": "do_something_for_query",
                    "parameters": ["https://github.com/Flow-Launcher/Flow.Launcher"]
                },
				"IcoPath": "Images\\app.png",
                "Score" : 0
			}]
		}
	));
}

if (method === "do_something_for_query") {
	url = parameters[0];
	do_something_for_query(url);
}

function do_something_for_query(url) {
	open(url);
}
```

<br/>

### Query entry point 
`if (method === "query")`

This if statement checks the args passed via JSON-RPC, parsed with `const { method, parameters } = JSON.parse(process.argv[2])`. If `method` is `'query'`, the `console.log` block runs. The `result` property is an array, so it can hold one or many results.

### Assigning an action to your results  
`JsonRPCAction`

This specifies the method to run when the user selects the result.
In this example, selecting the result calls the `do_something_for_query` method with a URL that opens the Flow Launcher GitHub repo.

### Result score
The `score` field assigns a weight to a result: the higher the score, the higher the result appears in Flow's result list. Scores are usually between 0 and 100. Keep it at 0 if your plugin is usually triggered by an action keyword; with a global action keyword (`*`), the average weight is 50. Users can also adjust the score in Flow's plugin settings. Flow's own fuzzy search scores range from 0 to 100, so plugins using the global action keyword should stay in that range to blend in with other results. Flow also raises the score of results the user has selected before, matching them by `Title` and `SubTitle`, so keep those consistent between queries.

### Your plugin.json
If you haven't already, create a plugin.json file, which tells Flow how to load your plugin.

Place it in the top-level folder.

For what to include in your plugin.json, see the [plugin.json reference](/plugin.json.md).

## Release your plugin to Flow's Plugin Store 

To release your plugin, follow the instructions in Flow's [plugin repo](https://github.com/Flow-Launcher/Flow.Launcher.PluginsManifest).

## Good references to follow

Here are some plugins to use as a reference:
- Plugin Template https://github.com/Joehoel/flow-launcher-plugin-template-node
- Discord Timestamps https://github.com/Jessuhh/discord-timestamps-flowlauncher-plugin
- NPM Search https://github.com/gabrielcarloto/flow-search-npm