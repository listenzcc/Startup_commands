# Software makeup for a brand new machine

- [Software makeup for a brand new machine](#software-makeup-for-a-brand-new-machine)
  - [Anaconda](#anaconda)
    - [Quick start-up](#quick-start-up)
    - [Virtual Environments Usage](#virtual-environments-usage)
  - [Powershell](#powershell)
    - [Install packages](#install-packages)
    - [Current profile](#current-profile)
  - [VSCode](#vscode)
    - [Addons](#addons)

---

## Anaconda

> Envision a world where data scientists can regularly deploy AI and machine learning projects into production at scale, quickly delivering insights into the hands of decision-makers.  
> How would that impact your business?
> [Anaconda](https://www.anaconda.com/) Enterprise supports your organization no matter the size, easily scaling from a single user on one laptop to thousands of machines. No headaches, no IT nightmares.

### Quick start-up

Python env should be activated using `conda init <shell name>`.

### Virtual Environments Usage

Thanks to this [blog](https://janakiev.com/blog/jupyter-virtual-envs/), which gives me a quick understandable summary.

Virtual environment is a python feature [virtualenv](https://virtualenv.pypa.io/en/latest/).

```zsh
# For Python 2, you should install virtualenv to manage virtual environment
pip install --user virtualenv
# Create a virtual environment
virtualenv myenv

# For Python >= 3.3, you can create a virtual environment with venv, an already intergrated module
python -m venv myenv

# Activate the virtual environment using system sourcing methld like
source myenv/bin/activate  # In Unix system
. myenv/bin/activate  # In PowerShell (Not test yet)
```

Virtual environment in anaconda is simpler.

```powershell
# Create conda virtual environment
conda create -n myenv
# Use a specific Python version other than current
conda create -n myenv python=3.6
# Activate the virtual environment
conda activate myenv
# Remove an environment
conda env remove -n myenv
```

Setting up ipykernel for Jupyter to use virtual environment.

```powershell
# Install ipykernel
pip install --user ipykernel
# Add your virtual environment as a new jupyter kernel
python -m ipykernel install --user --name=myenv
# Output like following shows directory of kernel.json
Installed kernelspec myenv in /home/user/.local/share/jupyter/kernels/myenv
# List installed kernels
jupyter kernelspec list
# Uninstall a kernel
jupyter kernelspec uninstall myenv
```

A kernel.json file in the directory shows the kernel

```json
    {
        "argv": [
            "/home/user/anaconda3/envs/myenv/bin/python",  # Make sure the path is correct.
            "-m",
            "ipykernel_launcher",
            "-f",
            "{connection_file}"
        ],
        "display_name": "myenv",
        "language": "python"
    }
```

---

## Powershell

### Install packages

> PackageManagement (a.k.a. OneGet) is a new way to discover and install software packages from around the web.
> It is a manager or multiplexor of existing package managers (also called package providers) that unifies Windows package management with a single Windows PowerShell interface. With PackageManagement, you can do the following.
>
> - Manage a list of software repositories in which packages can be searched, acquired and installed
> - Discover software packages
> - Seamlessly install, uninstall, and inventory packages from one or more software repositories

There are suggestion packages can be found from [PowerShell Gallery](https://www.powershellgallery.com/).

| Name              | Description                                                                                                                                    |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| oh-my-posh        | A theme engine for Powershell in ConEmu inspired by the work done by Chris Benti on PS-Config and Oh-My-ZSH on OSX and Linux (hence the name). |
| DirColors         | Provides dircolors-like functionality to all System.IO.FilesystemInfo formatters.                                                              |
| cd-extras         | cd conveniences from bash and zsh.                                                                                                             |
| PowerTab          | A module that enhances PowerShell's tab expansion.                                                                                             |
| PersistentHistory | Incremental history tracking.                                                                                                                  |

Some notes:

- Use `-Scope CurrentUser` for current user only.
- Use `Import-Module <name>` to enable module.
- Use `$PROFILE` to find out the path of `startup scripts`,  
  The file named as `Microsoft.PowerShell_profile.ps1` takes effect only on Win10 native powershell app.  
  Creat a file named as `profile.ps1` besides it, that is an overall effective startup script.

### Current profile

```powershell
# Import modules
Import-Module cd-extras
Import-Module Dircolors
Import-Module PersistentHistory
# Import-Module posh-git
Import-Module oh-my-posh
Import-Module PSEverything
Set-Theme Honukai

# Save Command History
$HistoryPath = "$env:USERPROFILE\Documents\WindowsPowerShell\History"
If (Test-Path "${HistoryPath}\History.csv") {
    Import-Csv "${HistoryPath}\History.csv" | Add-History
}
ElseIf (!(Test-Path $HistoryPath)) {
    New-Item -Path $HistoryPath -ItemType Directory
}
Register-EngineEvent -SourceIdentifier powershell.exiting -SupportEvent -Action { Get-History | Select-Object -Last 100 | Export-Csv -Path "${HistoryPath}\History.csv" }

# Mkdir Passed Pathes
$PassedPath = "$env:USERPROFILE\Documents\WindowsPowerShell\PassedPath"
If (!(Test-Path $PassedPath)) {
    New-Item -Path $PassedPath -ItemType Directory
}

# Init ObjShell
$ObjShell = New-Object -COM WScript.Shell

# Parse lnk into legal path
Function Parse_Lnk($LnkName) {
    # Current dir
    $cwd = (Get-Location).ToString()
    # Path of file as {Lnk}
    $Lnk = $ObjShell.CreateShortcut("$cwd/$LnkName")
    # Return full name of the path
    $Lnk.TargetPath
}

# CD to $PassedPath,
# and ready to go passed pathes
Function CD_PassedPath() {
    # Stack current path
    Set-LocationEx $PassedPath

    # Get all lnks, and lastest 5 lnks
    $Lnks = Get-ChildItem | Sort-Object LastWriteTime
    $Lnks_Last = $Lnks | Select-Object Name -Last 5
    $Lnks

    # List lastest 5 lnks
    # Make sure the lastest is the first
    [array]::Reverse($Lnks_Last)
    For ($i = 4; $i -ge 0; $i--) {
        $name = Parse_Lnk $Lnks_Last[$i].Name
        "[$i] $name"
    }

    # Let user choose path to enter,
    # return to current path if input is null.
    $j = Read-Host "Enter idx to cd"
    If (!($j -eq '')) {
        $name = Parse_Lnk $Lnks_Last[$j].Name
        CD_SaveLnk $name
    }
    else {
        Undo-Location
    }
}

# CD to dir or leaf(file)
Function CD_Dir_or_Leaf($Name) {
    # If $Name is invalid dir, throw error.
    If (!(Test-Path $Name)) {
        throw "Invalid path $Name."
    }
    # Make $Dir,
    # use directory if it is file.
    If (Test-Path $Name -PathType Leaf) {
        $Dir = (Get-Item $Name).Directory
    }
    Else {
        $Dir = $Name
    }
    # CD to $Dir
    Set-LocationEx $Dir
}

# CD and save lnk into $PassedPath
Function CD_SaveLnk() {
    # Enter HOME if input is null
    If ($args.get_length() -eq 0) {
        Set-LocationEx $env:USERPROFILE
    }
    # Enter into the first input,
    # parse lnk if needed
    ElseIf ($args[0].endsWith('.lnk')) {
        Set-LocationEx (Parse_Lnk $args[0])
    }
    Else {
        CD_Dir_or_Leaf $args[0]
    }
    # Save current path into $PassedPath
    $PwdInstance = (Get-Location)
    $LnkName = $PwdInstance.ToString().Replace('\', '~').Replace(':', '')
    $Lnk = $ObjShell.CreateShortcut("$PassedPath\$LnkName.lnk")
    $Lnk.TargetPath = $PwdInstance.ToString()
    $Lnk.Save()
}

# Find pattern in history
Function Find_Pattern_in_History() {
    if ($args.get_length() -eq 0) {
        Get-History
    }
    else {
        Get-History | findstr $args
    }
}

Remove-Item Alias:\cd
Set-Alias -Name cd -Value CD_SaveLnk
Set-Alias -Name ch -Value CD_PassedPath
Set-Alias -Name ce -Value Undo-Location
Set-Alias -Name pl -Value Parse_Lnk
Set-Alias -Name hf -Value Find_Pattern_in_History

$RoamingStateDir = 'C:\Users\zcc\AppData\Local\Packages\Microsoft.WindowsTerminal_8wekyb3d8bbwe\RoamingState'

if ($pwd.Path -eq 'C:\Windows\system32') {
    Set-LocationEx
}

#region conda initialize
# !! Contents within this block are managed by 'conda init' !!
(& "C:\Users\zcc\Anaconda3\Scripts\conda.exe" "shell.powershell" "hook") | Out-String | Invoke-Expression
#endregion
```

---

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
        "<C-o>": false, // Editor open
        "<C-r>": false, // Editor history
    },
