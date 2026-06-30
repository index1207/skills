---
name: pave
description: >
  CLI tool for managing your PATH environment variable. Adds/removes directories or executables to PATH,
  lists current PATH entries, searches for executables, and outputs shell-specific PATH configuration.
  Use this skill whenever the user asks about "PATH", "path environment variable", "executable path",
  "where is X installed", "add to PATH", "remove from PATH", or any PATH-related task.
  Also use when the user mentions shell environment setup, finding installed tools, or configuring
  PATH in .bashrc, .zshrc, $PROFILE, etc. Trigger this skill proactively for any PATH management request.
---

# pave — PATH Environment Variable Manager

`pave` is a cross-platform CLI tool developed by Microsoft for intuitively managing the PATH
environment variable on Windows, macOS, and Linux. It is already installed on the system, so no
separate installation is needed.

## Basic Commands

### `pave list`
Lists all directories currently registered in PATH.

```
pave list
```

### `pave add <PATH>`
Adds a directory, file path, executable name, or the current directory to PATH.

```
pave add C:\Users\me\.cargo\bin       # Add directory path
pave add .                            # Add current directory
pave add rustup                       # Search by executable name and add
```

**Behavior by input type:**
- **Directory path**: Adds the directory directly to PATH. Prints a warning if already added.
- **File path**: Adds the file's parent directory to PATH (e.g., `pave add C:\tools\python.exe` → adds `C:\tools\`).
- **Executable name**: Searches the entire filesystem for the executable, then adds the discovered directory to PATH. If multiple results are found, an interactive picker is displayed.
- **`.` (current directory)**: Adds the current working directory to PATH.

**Options:**
- `--all`: Searches hidden folders and ignored directories (.git, node_modules, etc.) in addition to normal ones. The default mode excludes `node_modules`, `.git`, `__pycache__`, `.cache`, and `site-packages` from the search.

### `pave remove [PATH]`
Removes or blocks a directory from PATH.

```
pave remove C:\Users\me\AppData\Local\Programs\Python\Python311
pave remove                              # Interactive picker
```

**Behavior:**
- **Paths managed by pave**: Removes them directly.
- **Paths from shell config** (Unix): Blocks them instead of removing. These paths will not be added back.
- **System PATH entries** (Windows, without admin privileges): Shows a "cannot remove" message.

In interactive mode, pave distinguishes between paths it manages and paths that come from the environment variable (`$PATH`).

### `pave search <QUERY>`
Searches for executables across all PATH directories.

```
pave search python
pave search node
```

- Partial matches are supported. Searching for `py` returns `python`, `pipenv`, etc.
- On Windows, `.exe`, `.cmd`, `.bat`, `.com`, and `.ps1` extensions are recognized as executables.
- On Unix, files with the executable bit set are searched.

### `pave env <SHELL>`
Outputs a PATH configuration string for the specified shell.

Supported shells: `bash`, `zsh`, `fish`, `xonsh`, `nushell`, `pwsh`

```
pave env bash    # export PATH="..." format
pave env zsh     # export PATH="..." format
pave env fish    # set -gx PATH "..." format (one per line)
pave env pwsh    # $env:PATH = "..." format (Windows semicolon-separated)
pave env xonsh   # $PATH = ... format (one per line)
pave env nushell # $env.PATH = ... format (one per line)
```

**Shell-specific configuration examples:**

| Shell | Config File | Command |
|---|---|---|
| Bash | `~/.bashrc` | `export PATH="$(pave env bash)"` |
| Zsh | `~/.zshrc` | `export PATH="$(pave env zsh)"` |
| Fish | `~/.config/fish/config.fish` | `set -gx PATH (pave env fish)` |
| PowerShell | `$PROFILE` | `$env:PATH = (pave env pwsh)` |
| Xonsh | `~/.xonshrc` | `$PATH = $(pave env xonsh).strip().split('\n')` |
| Nushell | `$nu.env-path` | `$env.PATH = (pave env nushell \| lines)` |

Adding this line to your shell config file makes the configuration permanent.

## Platform-Specific Behavior

### Windows
- `pave add`/`remove` writes directly to the **registry** (HKCU\Environment) and broadcasts a `WM_SETTINGCHANGE` message.
- PATH is reflected in all new terminal windows without needing to add shell config file entries.
- `pave env` converts MSYS/WSL paths via cygpath.

### macOS/Linux
- `pave` stores managed paths in a config file (`~/.config/pave/paths`).
- `pave env` removes pave-added paths from shell config files and reorganizes only the managed paths to provide a consistent PATH.
- Removing a shell config path with `pave remove` **blocks** it.

## Usage Workflow

### 1. Check PATH
```
pave list
```
Shows all currently registered directories.

### 2. Find an Executable
When you don't know what is installed or where it lives:
```
pave search <program-name>
```

### 3. Add to PATH
Add a newly installed program's directory to PATH:
```
pave add <directory-path>
# Or search by executable name and add:
pave add rustup
# Add current directory:
pave add .
```

### 4. Remove from PATH
Remove unnecessary directories:
```
pave remove <directory-path>
# Or select interactively:
pave remove
```

### 5. Make Permanent in Shell
Add the following line to your shell config file (`~/.bashrc`, `~/.zshrc`, `$PROFILE`, etc.).
Every time the shell starts, `pave env <shell>` runs and applies the latest PATH.

```bash
# Bash (~/.bashrc)
export PATH="$(pave env bash)"

# Zsh (~/.zshrc)
export PATH="$(pave env zsh)"

# Fish (~/.config/fish/config.fish)
set -gx PATH (pave env fish)

# PowerShell ($PROFILE)
$env:PATH = (pave env pwsh)
```

On Windows, no shell config file addition is needed since pave writes directly to the registry.

## Usage Scenarios

**Scenario 1: Setting PATH after Python installation**
```bash
# Search for where Python is located
pave search python

# If not found, add the installation path
pave add C:\Users\me\AppData\Local\Programs\Python\Python312

# To make it permanent in zsh, add the following line to ~/.zshrc:
export PATH="$(pave env zsh)"
```

**Scenario 2: Installing Rust (search by executable name)**
```bash
# Search for rustup and add it to PATH
pave add rustup

# Select the desired directory from the interactive picker
```

**Scenario 3: Cleaning up unnecessary PATH entries**
```bash
# Check current PATH
pave list

# Select directories to remove interactively
pave remove
```

**Scenario 4: Adding current project directory to PATH**
```bash
# From the project root
pave add .

# The project directory is now included in PATH for both the current session and new terminals
```

## Notes

- `pave add` takes effect immediately in the current session's PATH.
- To make changes permanent, add a line in the `export PATH="$(pave env <shell>)"` format to your shell config file.
- On Windows, `pave.exe` must be in the system PATH. If installed via WinGet, it is registered automatically.
- `pave remove` supports interactive mode, which is helpful when you are unsure which directory to remove.
- Passing a file to `pave add` automatically adds its parent directory to PATH.
- Adding a path that is already in PATH prints a warning message.
- Using `pave add --all` searches hidden folders and `.gitignore`-excluded items as well.
- On Unix, removing a shell config path with `pave remove` blocks it so it will not be added back.
