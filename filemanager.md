## Custom File Manager
To use a file manager other than the default Windows File Explorer, set it under Settings > General > Default File Manager. The values to use for each file manager are listed below.

`%d` is the directory path, and `%f` is the file path used when opening a file. `-select`, supported by some file managers, highlights the file or scrolls it into view when its location opens.

### Files
docs: https://files.community/docs/contributing/updates
```text
Path : Files or Files-stable
Arg For Folder : "%d"
Arg For File : -select "%f"
```

### Directory Opus
docs: https://www.gpsoft.com.au/help/opus11/index.html#!Documents/Go1.htm
```text
Path : (installed path)dopusrt.exe
Arg For Folder : /cmd Go "%d" NEW
Arg For File : /cmd Go "%f" NEW
```

### Total Commander
docs: https://www.ghisler.ch/wiki/index.php/Command_line_parameters
```text
Path : (installed path)TOTALCMD64.exe
Arg For Folder : /O /A /S /T "%d"
Arg For File : /O /A /S /T "%f"
```