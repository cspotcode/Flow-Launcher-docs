Flow is written in C#, so .NET plugins communicate with Flow directly, without an extra protocol.

## Initialisation

For C# plugins, we recommend generating your plugin from the [dotnet template](https://github.com/Flow-Launcher/dotnet-template).

A Flow .NET plugin directory needs at least two files:
1. [`plugin.json`](/plugin.json.md)
2. A .NET Assembly that implements **[IPlugin](/API-Reference/Flow.Launcher.Plugin/IPlugin.md)** or **[IAsyncPlugin](/API-Reference/Flow.Launcher.Plugin/IAsyncPlugin.md)** (reference the [Flow.Launcher.Plugin](https://www.nuget.org/packages/Flow.Launcher.Plugin/) NuGet package). The plugin template adds the reference and creates a `Main.cs` that implements `IPlugin`.

See the [Flow Launcher Plugin API Reference](/API-Reference/Flow.Launcher.Plugin.md).


See the [Flow Launcher C# plugin samples](https://github.com/Flow-Launcher/plugin-samples).

## IPlugin/IAsyncPlugin

The `Main` class that implements **[IPlugin](/API-Reference/Flow.Launcher.Plugin/IPlugin.md)** or **[IAsyncPlugin](/API-Reference/Flow.Launcher.Plugin/IAsyncPlugin.md)** handles search queries from Flow.

The **[IPlugin](/API-Reference/Flow.Launcher.Plugin/IPlugin.md)** interface has two required methods:
1. `void Init(PluginInitContext context)`
    - [PluginInitContext](/API-Reference/Flow.Launcher.Plugin/PluginInitContext.md) exposes part of Flow's API and a metadata object for your plugin.
    - It runs before `Query`, so do any preparation here.
    - Do expensive work here rather than in the constructor, because this method runs in parallel with other plugins.
2. `List<Result> Query(Query query)`
    - `Query` is invoked when the user activates this plugin with its action keyword.
    - It returns a `List` of [Result](/API-Reference/Flow.Launcher.Plugin/Result.md) objects.
 
 **[IAsyncPlugin](/API-Reference/Flow.Launcher.Plugin/IAsyncPlugin.md)** is the async version of **[IPlugin](/API-Reference/Flow.Launcher.Plugin/IPlugin.md)**
 - Instead of `Init` and `Query`, implement `InitAsync` and `QueryAsync`, which return `Task` and `Task<List<Result>>` so you can use `async/await`
 - `QueryAsync` receives a `CancellationToken token` that lets you check whether the user has typed a new query.


## Additional interfaces

Besides **IPlugin/IAsyncPlugin**, plugins can implement a series of interfaces that belong to **IFeatures**, for more interaction with Flow.

**Remarks**: Implement these interfaces in the same class that implements **IPlugin/IAsyncPlugin**.

### [IContextMenu](/API-Reference/Flow.Launcher.Plugin/IContextMenu.md)

`LoadContextMenus` is invoked when the user opens a result's context menu.
It returns results, like `Query/QueryAsync`.

### [IReloadable](/API-Reference/Flow.Launcher.Plugin/IReloadable.md)/[IAsyncReloadable](/API-Reference/Flow.Launcher.Plugin/IAsyncReloadable.md)

`ReloadData/ReloadDataAsync` is invoked when the user runs the `Reload Plugin Data` command from the _sys_ plugin. It's typically used to reload caches (such as the program information cached by the _Program_ plugin).

### [IPluginI18n](/API-Reference/Flow.Launcher.Plugin/IPluginI18n.md)

**IPluginI18n** marks the plugin as internationalized, so Flow loads its language resources from `/Languages` when loading the plugin.
With this interface and the language files in place, get translated text with `IPublicAPI.GetTranslation(string key)`.

#### Language Resource

A Language Resource file is named after its Language Code, with the suffix `.xaml`. The Language Codes are listed in [AvailableLanguages.cs](https://github.com/Flow-Launcher/Flow.Launcher/blob/dev/Flow.Launcher.Core/Resource/AvailableLanguages.cs).
The Language Resource file is a list of **key/value** pairs. Follow the examples in [en.xaml](https://github.com/Flow-Launcher/Flow.Launcher/blob/dev/Flow.Launcher/Languages/en.xaml).

#### Remark

Plugins must implement **IPluginI18n** for Flow to load their Language resources.
 
### [IResultUpdated](/API-Reference/Flow.Launcher.Plugin/IResultUpdated.md)


**IResultUpdated** lets a plugin return part of its query results early, which is useful for long-running queries.

To return results early, invoke the `ResultsUpdated` event with a `ResultUpdatedEventArgs`, which includes the current `Query` object and the List of `Result` objects similar to the return value in `Query(Async)`.

### [IDisposable](https://docs.microsoft.com/en-us/dotnet/api/system.idisposable) _Flow 1.8.0 or higher_

Implement **IDisposable** to dispose of unmanaged resources in the plugin. Flow calls `Dispose()` when it exits.
