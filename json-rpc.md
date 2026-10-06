## Introduction

> [JSON-RPC](https://en.wikipedia.org/wiki/JSON-RPC) is a remote procedure call protocol encoded in JSON.

In Flow Launcher, we use JSON-RPC as a **local** procedure call protocol to bind Flow and other program languages ([**Python plugin**](/py-develop-plugins.md) and [**JavaScript/TypeScript plugin**](/nodejs-develop-plugins.md)).

So we need to build a **common API** between Flow and Plugin.

![JSON-RPC](/assets/jsonrpc.png)

This page is the protocol reference. The source of truth is Flow's implementation in [`Flow.Launcher.Core/Plugin`](https://github.com/Flow-Launcher/Flow.Launcher/tree/dev/Flow.Launcher.Core/Plugin).

### Protocol versions

The plugin's `Language` in [plugin.json](/plugin.json.md) selects the protocol version:

| Version | `Language` values | Process | Message format |
|---|---|---|---|
| v1 | `Python`, `JavaScript`, `TypeScript`, `Executable` | A new process for every request | JSON-RPC-like; request in a command-line argument, response on stdout |
| v2 | `Python_v2`, `JavaScript_V2`, `TypeScript_V2`, `Executable_V2` | One long-running process | [JSON-RPC 2.0](https://www.jsonrpc.org/specification) over stdin/stdout |

`Language` values are case-insensitive.

---

### Version 1

#### How Flow runs the plugin

For each request, Flow starts the plugin with the request JSON as the last command-line argument:

| Language | Command |
|---|---|
| Python | `python.exe -c <bootstrap> <request>`, where the bootstrap adds the plugin folder and its `lib` and `plugin` folders to `sys.path` (Flow 1.20.0 and later) and runs `ExecuteFileName`; a `.pyz` file runs as `python.exe -B <ExecuteFileName> <request>`. Either way, the request is `sys.argv[1]` |
| JavaScript / TypeScript | `node.exe <ExecuteFileName> <request>` |
| Executable | `<ExecuteFileName> <request>` |

The plugin writes a single JSON response to **stdout** and exits. Any output on **stderr** is treated as an error and the response is discarded, so write debug output to a log file.

v1 is not spec-compliant JSON-RPC: requests use `parameters` instead of `params`, there is no `jsonrpc` member, and responses aren't matched by `id`.

#### Request

```json
{"id": 1, "method": "query", "parameters": ["search text"], "settings": {"apiKey": "..."}}
```

| Member | Description |
|---|---|
| `method` | `query`, `context_menu`, or the name of one of the plugin's own action methods |
| `parameters` | Array of arguments; see below |
| `settings` | The plugin's current settings, if it has a [settings template](/json-rpc-settings.md) |
| `id` | Request counter; can be ignored |

| `method` | `parameters` |
|---|---|
| `query` | `[search]`: the query text after the action keyword |
| `context_menu` | `[contextData]`: the `ContextData` of the selected result |
| your action method | the `parameters` of the selected result's `JsonRPCAction` |

#### Response to `query` and `context_menu`

```json
{
  "result": [
    {
      "Title": "Hello",
      "SubTitle": "Press Enter to open example.com",
      "IcoPath": "Images/app.png",
      "Score": 0,
      "ContextData": ["any", "data"],
      "JsonRPCAction": {
        "method": "open_url",
        "parameters": ["https://example.com"],
        "dontHideAfterAction": false
      }
    }
  ],
  "debugMessage": "",
  "settingsChange": {}
}
```

- `result`: the results. Each result supports the fields of [Result](/API-Reference/Flow.Launcher.Plugin/Result.md), plus `JsonRPCAction` (the action to run when the result is selected) and `SettingsChange`.
- `debugMessage` (optional): if not empty, Flow shows it in a message box.
- `settingsChange` (optional): settings values to update.

Member names are case-insensitive.

#### Actions

When the user selects a result, Flow runs its `JsonRPCAction`:

- If `method` starts with `Flow.Launcher.`, Flow calls that [Flow Launcher API](/json-rpc.md?id=flow-launcher-api) method directly.
- Otherwise, Flow runs the plugin again with that `method` and `parameters`. The plugin may print nothing, or a request such as `{"method": "Flow.Launcher.ChangeQuery", "parameters": ["wiki cats", true]}` to call a Flow Launcher API method.

Flow hides its window after the action unless `dontHideAfterAction` is `true`.

#### Flow Launcher API

Methods of [IPublicAPI](/API-Reference/Flow.Launcher.Plugin/IPublicAPI.md) can be called as `Flow.Launcher.<MethodName>`. **Pass every parameter of the method, including optional ones.** Flow looks up the method by the exact number and types of the parameters, and silently ignores calls that don't match.

```json
{"method": "Flow.Launcher.ChangeQuery", "parameters": ["wiki cats", false]}
```

Commonly used methods:

| method | parameters |
|---|---|
| `Flow.Launcher.ChangeQuery` | `[query, requery]` |
| `Flow.Launcher.OpenUrl` | `[url, inPrivate]` |
| `Flow.Launcher.OpenDirectory` | `[directoryPath, fileToSelect]` |
| `Flow.Launcher.CopyToClipboard` | `[text, directCopy, showDefaultNotification]` |
| `Flow.Launcher.ShellRun` | `[cmd, filename]`, e.g. `["dir", "cmd.exe"]` |
| `Flow.Launcher.ShowMsg` | `[title, subTitle, iconPath]` |
| `Flow.Launcher.ShowMainWindow` / `HideMainWindow` | `[]` |
| `Flow.Launcher.RestartApp` | `[]` |
| `Flow.Launcher.SaveAppAllSettings` | `[]` |
| `Flow.Launcher.CheckForNewUpdate` | `[]` |
| `Flow.Launcher.OpenSettingDialog` | `[]` |
| `Flow.Launcher.ReloadAllPluginData` | `[]` |
| `Flow.Launcher.StartLoadingBar` / `StopLoadingBar` | `[]` |

---

### Version 2

#### How Flow runs the plugin

Flow starts the plugin once, with the plugin folder as the working directory, and keeps it running. Flow and the plugin exchange [JSON-RPC 2.0](https://www.jsonrpc.org/specification) messages over stdin/stdout. Both sides can send requests.

| Language | Command | Message framing |
|---|---|---|
| Python_v2 | `python.exe -c <bootstrap>`, which adds the plugin folder and its `lib` and `plugin` folders to `sys.path` and runs `ExecuteFileName` (a `.pyz` file is run directly) | One JSON message per line |
| JavaScript_V2 / TypeScript_V2 | `node.exe <ExecuteFileName>` | `Content-Length` headers (as in the [Language Server Protocol](https://microsoft.github.io/language-server-protocol/specifications/base/0.9/specification/#baseProtocol)) |
| Executable_V2 | `<ExecuteFileName>` | One JSON message per line |

The environment variables `FLOW_VERSION`, `FLOW_PROGRAM_DIRECTORY`, and `FLOW_APPLICATION_DIRECTORY` are set for the plugin process. Flow serializes its requests with camelCase member names; member names in responses are case-insensitive.

#### Methods Flow calls

All parameters are positional (`params` is an array).

| method | `params` | Expected result |
|---|---|---|
| `initialize` | `[context]`: includes `currentPluginMetadata` | ignored |
| `query` | `[query, settings]`; `query` has `search`, `trimmedQuery`, `originalQuery`, `actionKeyword`, `searchTerms`, `isReQuery`, `isHomeQuery` | `{"result": [ … ]}`, same as the v1 response |
| `context_menu` | `[contextData]` | `{"result": [ … ]}` |
| your action method | `[parameters]`: the `JsonRPCAction.parameters` array as a **single** argument | `{"hide": true}` to hide Flow's window (the default), `{"hide": false}` to keep it open |
| `reload_data` | `[context]` | optional; called on `reload plugin data` |
| `close` | `[]` | optional; called before Flow closes the plugin |

Example `query` request from Flow:

```json
{"jsonrpc":"2.0","id":2,"method":"query","params":[{"search":"cats","actionKeyword":"wiki","trimmedQuery":"wiki cats","searchTerms":["cats"]},{"apiKey":"..."}]}
```

and the plugin's response:

```json
{"jsonrpc":"2.0","id":2,"result":{"result":[{"title":"Hello","subTitle":"…","jsonRPCAction":{"method":"open_url","parameters":["https://example.com"]}}]}}
```

In v2, `Flow.Launcher.*` methods in `JsonRPCAction` are **not** handled by Flow; every action is sent to the plugin. To call Flow's API, the plugin sends its own request.

#### Methods the plugin can call

The plugin can send requests to Flow for the methods of Flow's [JSON-RPC public API](https://github.com/Flow-Launcher/Flow.Launcher/blob/dev/Flow.Launcher.Core/Plugin/JsonRPCV2Models/JsonRPCPublicAPI.cs), for example:

```json
{"jsonrpc":"2.0","id":1,"method":"ChangeQuery","params":["wiki cats", false]}
```

- Method names are the C# names, **case-sensitive** (`ChangeQuery`, `ShowMsg`, `OpenUrl`, `CopyToClipboard`, `LogInfo`, `HttpGetStringAsync`, …). The `Async` suffix is optional.
- Optional parameters can be omitted.
- `UpdateResults` with `[trimmedQuery, {"result": [ … ]}]` replaces the results shown for that query, which is useful for long-running searches.
