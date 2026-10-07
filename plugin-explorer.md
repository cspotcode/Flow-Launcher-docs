### Explorer Plugin

The Explorer plugin is a default plugin that installs with Flow Launcher and searches for files and folders on your filesystem. You can choose which search engine handles each type of search: the built-in Windows Search Index (the default, part of Windows) or [Everything](https://www.voidtools.com/), a popular third-party tool. Everything is usually faster and offers more search options than Windows Index, but you need to download and install it. If you choose Everything as your search index engine and don't have it installed, Flow can install it for you: run a search and click the "Warning: Everything is not running" result.

Here is how to configure this plugin:

#### General Setting tab
----
![General Setting tab](/assets/explorer_1.png)

- *Use search result's location as the working directory of the executable* : tick this to run applications launched through the Explorer plugin with their own directory as the working directory
- *Hit enter to open folder in Default File Manager* : tick this to have ENTER open the directory in the default file manager, instead of browsing into it in Flow.
- *File editor path* and *Folder editor path* : set a program for opening files or folders from a search result's context menu (right-click / SHIFT + ENTER). These options only appear in the context menu if you set them here.
For example, to open any directory in Visual Studio Code, first add the path to your Visual Studio Code installation in the Folder editor path setting:

![Folder editor path option](/assets/explorer_1a.png)

then search for a folder and right-click it (or press SHIFT + ENTER):

![Example folder search](/assets/explorer_1b.png)

and choose the *Open With Editor* option

![Context menu example](/assets/explorer_1c.png)

- *Shell path* : the shell launched from a result's context menu (right-click / SHIFT + ENTER). Defaults to the Windows `cmd`
- *Index search engine*, *Content search engine* and *Directory recursive search engine*: choose Windows Index or Everything (if installed) for each task. Index is for general filename search, Content is for searching within text files, and Directory recursive is for listing a directory's subdirectories.
- *Open Windows index option*: this opens the Windows Indexing system setting, so you can see and adjust exactly what the Windows Index service is doing.
#### Everything Setting tab
----
![Everything Setting tab](/assets/explorer_2.png)

- *Search full path* : search the full path, not just the filename. Equivalent to the `path:` modifier within Everything.
- *Sort Option* : how the search results are sorted.
- *Everything Path* : Flow Launcher tries to find your Everything installation automatically. If that fails, or Everything is installed in a non-standard directory, specify its path here.

**NOTE**
If Flow downloads Everything for you, it installs version 1.4.1.1009. To use the Everything 1.5 alpha branch instead:

1. Fully exit Everything (right-click the Everything system tray icon and click Exit)
2. Open the Everything-1.5a.ini file in the same folder as Everything64.exe
3. Add the following line to the end of the file:
   ```ini
   alpha_instance=0
   ```
4. Save changes and restart Everything.

Everything then stops using an instance name for window classes (IPC), but keeps using the 1.5a instance name for settings, data, and the Everything Service.
(source - https://github.com/Flow-Launcher/Flow.Launcher/issues/1716)

#### Customised Action Keywords tab
----
![Customise Action Keywords tab](/assets/explorer_3.png)

- Each option sets a custom keyword for one type of search. Searches set to `*` run whenever you type anything in Flow Launcher, or when you give the Explorer plugin a global keyword. For example, searching within documents defaults to `doc:`, because searching document contents is slow and should only run when you mean to. If you don't care which type of search runs, use the 'Search' keyword.

#### Quick Access Links tab
----
![Quick Access Links tab](/assets/explorer_4.png)

- Add directories you work with often, so they're returned as soon as you start typing their location. You can also add or remove directories through the context menu (right-click / SHIFT + ENTER) of any directory search result.
#### Index Search Excluded Paths tab
----
![Index Search Excluded Paths tab](/assets/explorer_5.png)

- Adding a directory here excludes it from Flow Launcher's search, overriding any Windows Indexing or Everything settings.

#### Using Everything

When searching with Everything, [these Everything commands](https://www.voidtools.com/support/everything/searching/) are a useful reference.
