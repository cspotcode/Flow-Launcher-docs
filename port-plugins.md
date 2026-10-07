## Wox/PowerToys Run C# Plugins

### Notes

- When porting, please keep the author's commit history
- Flow Launcher targets .NET 9 or later, so upgrade older plugins to keep them maintainable
- Include every DLL the plugin uses in the final build. To do this, set `CopyLocalLockFileAssemblies` to `true` in your project file

### Steps

1. Fork the repo or create a new one; either way, keep the project's commit history. A fork can be updated directly. For a new repo, clone the original repo, add your new repo as a remote, remove the original remote, and push
2. Use `try-convert` tool from https://github.com/dotnet/try-convert
3. `try-convert -w path-to-folder-or-solution-or-project`
4. Fix up the project file if needed. A good template to follow is the [Explorer plugin](https://github.com/Flow-Launcher/Flow.Launcher/blob/dev/Plugins/Flow.Launcher.Plugin.Explorer/Flow.Launcher.Plugin.Explorer.csproj) project:
    - fix `<TargetFramework>` to `net9.0-windows10.0.19041.0`
    - set the output location as `Output\Release\<name of the project>`
    - add `<CopyLocalLockFileAssemblies>true</CopyLocalLockFileAssemblies>` and `<AppendTargetFrameworkToOutputPath>false</AppendTargetFrameworkToOutputPath>` to the csproj file
    - bump version to 2.0.0 and fix up any missing attributes if necessary
5. Update the code, and fix the plugin's settings layout if necessary
6. Update the readme to say where the port comes from and who the original author is

## Wox Python Plugins

### Notes

- When porting, please keep the author's commit history

### Steps

1. Change the import from Wox to import from flowlauncher
2. Make the class inherit from FlowLauncher instead of Wox
3. Install the flowlauncher python package
