### Testing your plugin

To test a plugin locally after building it, move the output files into Flow Launcher's `FlowLauncher\Plugins` directory, which the `userdata` command opens. For .NET (C# or F#) plugins, you can instead have your IDE build directly to that location (but don't commit that build output path to Git).


### Detail Steps

1. Run `userdata` in Flow Launcher.
2. Navigate into the `Plugins` folder.
3. Move any existing plugin with the same `Plugin ID` (as specified in `plugin.json`) out of the folder (_**if more than one plugin has the same `Plugin ID`, only the one with the highest `Version` is loaded; if their versions are equal, none of them are loaded**_).
4. Copy and paste the newly built plugin folder into this folder.
5. Run `Restart Flow Launcher` to load the new plugin.

Tip: .NET plugins (C# and F#) require restarting Flow after every rebuild. Python and JS/TS plugins can be edited in place.
