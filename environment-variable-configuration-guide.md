# Environment variable configuration guide

This topic explains how to efficiently and correctly configure environment variables in macOS, Windows, and Linux, so you can avoid writing sensitive information such as API keys and access keys in plaintext in your code.


|Operating system |Shell |Temporary (set in current session) |Permanent (set in recommended file) |
|---|---|---|---|
|**macOS / Linux** |Bash / Zsh |`export ARK_API_KEY="value"` |`~/.zshenv` or `~/.bash_profile` |
| |PowerShell |`$env:ARK_API_KEY = "value"` |`Microsoft.PowerShell_profile.ps1` |
|**Windows** |Command prompt |`set ARK_API_KEY=value` |`setx ARK_API_KEY "value"` |
| |PowerShell |`$env:ARK_API_KEY = "value"` |`[Environment]::SetEnvironmentVariable(...)` |


<span id="a145656a"></span>
## Concepts

Environment variables are operating system\-level key\-value pairs used to store configuration information and can affect the behavior of running processes. They are divided into two types:


* **Temporary variables**: Valid only in the terminal session in which they are created. They become invalid after the terminal is closed.

* **Permanent variables**: Persistently stored in system configuration files and remain valid in new terminal sessions or after a system restart.


<span id="42cfcec5"></span>
## macOS & Linux configuration instructions

On Unix\-like systems, environment variable configuration is closely related to the shell you use.

<span id="2f4b1943"></span>
### Understand shell configuration files


* `~/.bash_profile`: Loaded when Bash starts as a login shell. It is usually used to define variables that should be available throughout the user session.

* `~/.bashrc`: Loaded when Bash starts as an interactive non\-login shell. Suitable for defining aliases and functions.

* `~/.zshenv`:  **(Recommended for environment variables)**  Loaded every time Zsh starts, regardless of whether it is a login or interactive shell. This is the most reliable place to set environment variables.

* `~/.zprofile`: Loaded when Zsh starts as a login shell, after `.zshenv`.

* `~/.zshrc`: Loaded when Zsh starts as an interactive shell. Suitable for configuring shell interaction\-related settings, such as prompts and aliases.


<div data-tips="true" data-tips-type="tip" data-tips-is-title="true">Tip</div>


<div data-tips="true" data-tips-type="tip">To ensure that environment variables are available in all scenarios, including GUI applications, we recommend putting <code>export</code> statements in <code>~/.zshenv</code> (Zsh) or <code>~/.bash_profile</code> (Bash).</div>


<span id="a8329a39"></span>
### Bash / Zsh configuration

<span id="e9350dae"></span>
#### Temporary configuration

Run the `export` command in the terminal.

```Bash
# Example: Temporarily set ARK_API_KEY
export ARK_API_KEY="your-ark-api-key-here"
```


<span id="37c37c61"></span>
#### Permanent configuration


1. Open the terminal and use the following command to view the default shell type.

   ```Bash
   echo $SHELL
   
   # If /bin/zsh is returned: The default shell type is Zsh (Z shell).
   
   # If /bin/bash is returned: The default shell type is Bash.
   ```
   

2. Open your shell configuration file. For Zsh users, `~/.zshenv` is recommended.

   ```Bash
   # Zsh users
   nano ~/.zshenv
   
   # Bash users
   nano ~/.bash_profile
   ```
   

3. Add the `export` statement to the end of the file.

   ```Bash
   # Set the ARK_API_KEY environment variable
   export ARK_API_KEY="your-ark-api-key-here"
   ```
   

4. Save the file, then run the `source` command or restart the terminal for the configuration to take effect.

   ```Bash
   # Zsh users
   source ~/.zshenv
   
   # Bash users
   source ~/.bash_profile
   ```
   


<span id="a1ce8851"></span>
### PowerShell (macOS / Linux)

PowerShell behaves basically the same on all platforms.

<span id="174ea7f4"></span>
#### Temporary configuration

```PowerShell
$env:ARK_API_KEY = "your-ark-api-key-here"
```


<span id="bb5a3575"></span>
#### Permanent configuration


1. Edit the PowerShell profile.

   ```PowerShell
   # This automatically opens the profile in the default editor
   code $profile
   ```
   

2. Add the following line to the file:

   ```PowerShell
   $env:ARK_API_KEY = "your-ark-api-key-here"
   ```
   

3. Save the file, then run the `.` command or restart PowerShell.

   ```PowerShell
   . $profile
   ```
   


&nbsp;

<span id="e041d007"></span>
## Windows configuration instructions

Windows divides environment variables into "user variables" and "system variables." The former are valid only for the current user, while the latter are valid for all users.

<span id="fc935d23"></span>
### Command prompt (CMD)

<span id="4168a170"></span>
#### Temporary configuration

Use the `set` command.

```Bash
set ARK_API_KEY=your-ark-api-key-here
```


<span id="886575ad"></span>
#### Permanent configuration

Use the `setx` command.

```Bash
:: Set a user variable
setx ARK_API_KEY "your-ark-api-key-here"

:: Set a system variable (requires administrator privileges)
setx ARK_API_KEY "your-ark-api-key-here" /m
```


**Note**: Variables set by `setx` take effect only in a **new** command prompt window and do not affect the current window.

<span id="83f57ec4"></span>
### PowerShell

<span id="62cc96e9"></span>
#### Temporary configuration

```PowerShell
$env:ARK_API_KEY = "your-ark-api-key-here"
```


<span id="4c7dda23"></span>
#### Permanent configuration

The .NET API is the most reliable method.

```PowerShell
# Set a user variable
[Environment]::SetEnvironmentVariable("ARK_API_KEY", "your-ark-api-key-here", "User")

# Set a system variable (requires administrator privileges)
[Environment]::SetEnvironmentVariable("ARK_API_KEY", "your-ark-api-key-here", "Machine")
```


<span id="60200b14"></span>
### Graphical user interface (GUI)


1. Search for "Edit the system environment variables" in the Start menu and open it.

2. In the "System Properties" dialog box, click the "Environment Variables..." button.

3. In the pop\-up window, you can add or modify "user variables" for your user account in the upper section, or add or modify "system variables" for all users in the lower section.

4. Click "New..." and enter the variable name `ARK_API_KEY` and your key as the variable value.

5. Click "OK" in each dialog box.


&nbsp;

<span id="0b7661fa"></span>
## Verify the configuration

After the configuration is complete, you can verify it as follows:


* **macOS / Linux**:

   ```Bash
   echo $ARK_API_KEY
   ```
   

* **Windows (CMD)** :

   ```Bash
   echo %ARK_API_KEY%
   ```
   

* **PowerShell (all platforms)** :

   ```PowerShell
   echo $env:ARK_API_KEY
   # Or
   Get-ChildItem env:ARK_API_KEY
   ```
   


For permanent configuration, be sure to **restart your terminal or IDE** and check again to ensure that the configuration has been loaded correctly.

&nbsp;

<span id="4bc54785"></span>
## Advanced tips and practices

<span id="44b73a58"></span>
### Security: Protect your keys

**Warning**: Never commit configuration files that contain sensitive information such as `ARK_API_KEY` to version control systems such as Git.


* **Use**  **`.gitignore`**: Ensure that local configuration files containing sensitive information, such as `.envrc` and `.env`, are added to the `.gitignore` file.

* **Project\-level environment variables**: For a specific project, we recommend using a `.env` file to manage environment variables, and using libraries such as `dotenv` (Node.js) and `python-dotenv` (Python) to load them when the application starts.


<span id="f073edb7"></span>
### Directory\-level environment variables with `direnv`

`direnv` is a powerful tool that can automatically load or unload environment variables when you enter or leave a project directory.


1. **Install** (using macOS Homebrew as an example):

   ```Bash
   brew install direnv
   ```
   

2. **Configure**: Create a `.envrc` file in the project root directory.

   ```Bash
   # .envrc
   export ARK_API_KEY="your-project-specific-key"
   ```
   

3. **Authorize**: The first time you enter the directory, run `direnv allow`. After that, every time you enter the directory, the variables in `.envrc` automatically take effect.
