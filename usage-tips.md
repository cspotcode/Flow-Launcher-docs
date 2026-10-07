### Usage Tips

#### Installation
- Flow is published as a self-contained app, so it runs straight away without installing the .NET runtime. With portable mode, it can be stored and run from Dropbox or another cloud storage provider. The trade-off is a slightly bigger installation (around 250 MB), because it bundles the required .NET components.

#### Searching
- Search for items using:
  - Acronyms e.g. `gk` or `gp` for GitKraken Preview.
  - Fuzzy e.g. `acr` or `rea` for Acrobat Reader DC.
  - Single word e.g. `code` or `visual` for Visual Studio Code.
- Right-click a result, or press the right arrow key, to open its context menu with additional actions. Each plugin provides its own context menu.

#### Plugins
- Type `?` in the search bar to see the active action keywords. Type the first letter or two of a keyword to narrow the list.
- To prioritise a plugin's results, open the plugin's settings page and click the number next to 'Priority', under the plugin's title and description. The higher the priority, the higher the plugin's results appear in Flow's result list.
- To reload all plugin data, press `F5` in the query window or type `reload plugin data`.
- The Program and Bookmarks plugins detect changes automatically, so newly installed apps and new bookmarks appear soon after they're added.
- If a plugin isn't triggering, open Flow's settings, go to the Plugins tab, and check whether the plugin has a specific action keyword. Some plugins use a dedicated keyword to limit their results and avoid cluttering the list.
- On an Explorer plugin result, press `Ctrl + Shift + Enter` to open the folder directly instead of navigating into it.
- Save frequently used or favourite files and folders with the Explorer plugin: navigate to the file or folder, open the context menu, and select `Add to Quick Access`. This is especially handy with a custom Quick Access action keyword instead of the default '*': typing it lists your saved files and folders. Change the action keyword on the plugin's settings page.
- Press `Ctrl + Shift + Enter/Click` on a Shell plugin command to run it as Administrator.
- In the plugins download list, press `Ctrl + Enter/Click` to open the plugin's URL.
- Explorer's Search action keyword combines Path and Index search, so you can just type what you're looking for without choosing a keyword. The separate Path (search a specific path) and Index (search file and folder names) keywords run only that kind of search, and are disabled by default.
- When searching inside a directory, press `Ctrl + Backspace` to go up one level.

#### Settings
- Flow's settings, including installed plugins, are stored in:
  - If using roaming: `%APPDATA%\FlowLauncher`
  - If using portable, by default: `%localappdata%\FlowLauncher\app-<VersionOfYourFlowLauncher>\UserData`
- To back up Flow's settings and installed plugins, back up your UserData folder. To find it, type `flow launcher userdata` in the search bar.
- To restore saved settings, exit Flow, delete the current UserData folder, and copy yours in. When Flow starts, all your settings are restored. The exception is the Explorer plugin's saved Quick Access paths, which you may need to update if the locations have changed.
- When moving from a higher screen resolution to a lower one, you may need to adjust "SettingWindowWidth", "SettingWindowHeight", "SettingWindowTop", and "SettingWindowLeft", or the settings window may appear outside the visible area. Default values for 1920x1080: 1000, 700, 0, 0.
- To reset Flow to its default settings, close it and move or delete the UserData folder. Flow recreates the defaults.








