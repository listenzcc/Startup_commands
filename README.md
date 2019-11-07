# This is a software makeup for a brand new machine

## Anaconda

> Envision a world where data scientists can regularly deploy AI and machine learning projects into production at scale, quickly delivering insights into the hands of decision-makers.  
> How would that impact your business?
[Anaconda](https://www.anaconda.com/) Enterprise supports your organization no matter the size, easily scaling from a single user on one laptop to thousands of machines. No headaches, no IT nightmares.

Python env should be activated using `conda init <shell name>`.

## Powershell

### Install packages

> PackageManagement (a.k.a. OneGet) is a new way to discover and install software packages from around the web.
> It is a manager or multiplexor of existing package managers (also called package providers) that unifies Windows package management with a single Windows PowerShell interface. With PackageManagement, you can do the following.  
>
> - Manage a list of software repositories in which packages can be searched, acquired and installed
> - Discover software packages 
> - Seamlessly install, uninstall, and inventory packages from one or more software repositories 
There are suggestion packages can be found from [PowerShell Gallery](https://www.powershellgallery.com/).

| Name | Description |
| -------- | --------------------------------|
| oh-my-posh | A theme engine for Powershell in ConEmu inspired by the work done by Chris Benti on PS-Config and Oh-My-ZSH on OSX and Linux (hence the name).  |
| DirColors | Provides dircolors-like functionality to all System.IO.FilesystemInfo formatters. |
| cd-extras | cd conveniences from bash and zsh. |
| PowerTab | A module that enhances PowerShell's tab expansion. |
| PersistentHistory | Incremental history tracking. |

Use `-Scope CurrentUser` for current user only.

Use Import-Module _[name]_ to enable module.

## VSCode

> [Visual Studio Code](https://code.visualstudio.com/) is a lightweight but powerful source code editor which runs on your desktop and is available for Windows, macOS and Linux. It comes with built-in support for JavaScript, TypeScript and Node.js and has a rich ecosystem of extensions for other languages (such as C++, C#, Java, Python, PHP, Go) and runtimes (such as .NET and Unity). Begin your journey with VS Code with these [introductory videos](https://code.visualstudio.com/docs/introvideos/overview).

### Addons

- Visual Studio IntellCode
- vim
- Python
- TabNine
- Markdown
- Guides
- GitLens

Example settings.json as following:  

    // window
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    "team.showWelcomeMessage": false,
    "explorer.confirmDelete": false,
    "window.zoomLevel": 0,

    // python
    "python.pythonPath": "C:\\Users\\----\\Anaconda3\\pythonw.exe",
    "python.formatting.provider": "yapf",
    "vsintellicode.python.completionsEnabled": false,

    // git
    "git.autofetch": true,

    // editor
    "editor.fontSize": 16,
    "editor.rules": [80, 120],
    "editor.renderWhitespace": "all",
    "editor.suggestSelection": "first",
    "editor.renderControlCharacters": true,
    "editor.renderIndentGuides": false,

    // vim
    "vim.autoSwitchInputMethod.enable": true,
    "vim.autoSwitchInputMethod.obtainIMCmd": "C:\\Users\\----\\OneDrive\\apps\\im-select.exe",
    "vim.autoSwitchInputMethod.switchIMCmd": "C:\\Users\\----\\OneDrive\\apps\\im-select.exe {im}",
    "vim.autoSwitchInputMethod.defaultIM": "1033",
    "vim.easymotion": true,
    "vim.sneak": true,
    "vim.incsearch": true,
    "vim.useSystemClipboard": true,
    "vim.useCtrlKeys": true,
    "vim.hlsearch": true,
    "vim.insertModeKeyBindings": [
        {
        "before": ["j", "j"],
        "after": ["<Esc>"]
        }
    ],
    "vim.normalModeKeyBindings": [
        {
            "before": [";"],
            "after": [":"],
        }
    ],
    "vim.leader": "<space>",
    "vim.handleKeys": {
        "<C-a>": false, // select all
        "<C-f>": false, // search
        "<C-h>": false, // search and replace
        "<C-n>": false, // new file
        "<C-w>": false, // close file
        "<C-c>": false, // copy
        "<C-v>": false, // paste
    },
