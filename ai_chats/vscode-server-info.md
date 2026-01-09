# vscode-server-info (gemini-3-default)

## does vscode require root permissions to install vscode-server components on a remote linux system?
January 03, 2026

No, VS Code does not require root permissions to install its server components on a remote Linux system. 
User-Level Installation: By default, the VS Code Server is automatically installed into a hidden directory within your user's home folder, typically located at ~/.vscode-server.
Security Context: The server runs under the same user account you used to authenticate via SSH, ensuring it only has the same permissions as your user and cannot access system-level files without explicit permission.
Custom Installation Path: If your home directory is restricted (e.g., due to low disk space or no-exec permissions), you can configure a custom installation path by adding the "remote.SSH.serverInstallPath" setting in your local VS Code settings.json.
Permissions for Remote Files: While the installation does not require root, you will still need standard Linux permissions to read or write specific files on the remote system. If you need to edit system-level files, you must use a separate "Save as Root" extension or configure your SSH connection to log in as a privileged user. 




Where are VS Code remote server components installed?

What are common issues when using VS Code with Remote-SSH?

Tell me more about configuring a custom installation path

undefined
undefined
undefined
7 sites
Remote Development FAQ - Visual Studio Code
How do the Remote Development extensions secure access to a remote machine, VM, or container? Visual Studio Code Remote Developmen...

Visual Studio Code

SSH in VSCode: do I need to install anything on the remote ...
Paky9893. • 6y ago. It will install it into your home directory into a directory named . vscode-server so your users permissions s...

Reddit

How to change vscode-server directory - visual-studio - Stack Overflow
Unfortunately there is no direct way yet with VSCode to install into a custom directory. You could follow below steps to move or i...

Stack Overflow

Show all
## what are common issues when using VS Code with Remote-SSH?
January 03, 2026

Common issues when using VS Code with Remote-SSH often stem from network restrictions, resource limits, or configuration errors. 
1. Connection Failures and Timeouts
SSH Credentials and Firewalls: Incorrect hostnames, ports, or passwords often cause "Connection refused" errors. Ensure port 22 (or your custom port) is open and accessible.
Proxy and Network Barriers: Local proxy settings are not automatically reused on the remote host, which can block extensions from working unless manually configured.
Idle Disconnections: Connections may drop if the server closes idle sessions. This can be mitigated by enabling keep-alive settings in both VS Code ("remote.SSH.keepalive": 30) and your local SSH config (ServerAliveInterval 60). 
2. VS Code Server Installation Issues
Missing Dependencies: The remote system must have tar installed to unpack server components.
Unsupported Platforms: VS Code Remote-SSH does not support Alpine Linux or other non-glibc based distributions.
Offline/Restricted Servers: If the remote machine lacks internet access, the server installation will fail. You can enable "remote.SSH.allowLocalServerDownload" to download the server locally and transfer it via SCP. 
3. Resource and Memory Constraints
Insufficient RAM: The VS Code Server is resource-intensive. On low-power servers, it may crash or cause the system to lock up.
User Limits (ulimits): In institutional environments, strict per-user memory limits may prevent the server from allocating necessary memory (e.g., "Cannot allocate Wasm memory"). 
4. Authentication and Synchronization Bugs 
Repeated Password Prompts: If not using key-based authentication, you may be prompted multiple times. Using SSH Multiplexing (ControlMaster) on macOS/Linux clients can reduce these prompts.
Git Sync Hangs: If your SSH key has a passphrase, Git operations (pull/sync) within VS Code may hang. Using an SSH key without a passphrase or switching to HTTPS for Git is a common workaround. 
Recommended Troubleshooting
If you encounter persistent errors, try these common fixes:
Kill the Server: Use the command Remote-SSH: Kill VS Code Server on Host from the Command Palette to clear corrupted processes.
Manual Cleanup: SSH into the remote via terminal and run rm -rf ~/.vscode-server to force a clean re-installation.
Check Logs: View the detailed connection log in the Output panel (dropdown menu: Remote-SSH) to identify the exact point of failure. 




How can I automate VS Code server installation on restricted remote machines?

Tell me more about SSH Multiplexing for Remote-SSH

Tell me more about `tar` and Remote-SSH

undefined
undefined
undefined
21 sites
Remote Development using SSH - Visual Studio Code
Remote - SSH limitations * Using key based authentication is recommended. Passwords and other tokens entered for alternate authent...

Visual Studio Code

SSH connection refused: 6 effective methods to fix it - Hostinger
SSH connection refused: what it is, causes, and 6 effective methods to fix it * What is SSH connection refused? * What causes the ...

Hostinger

Why ssh connection to host via VS code is getting ...
Edit sshd_config and add: ClientAliveInterval 60. ClientAliveCountMax 5. Then restart SSH and sudo systemctl restart sshd. Enable ...

Microsoft Learn

Show all
## how much disk storage space does vscode-server require for a typical installation?
January 03, 2026

A typical VS Code Server installation on a remote Linux system requires approximately 150 MB to 700 MB of initial disk storage space. However, this can grow significantly over time as you add extensions and work on different projects. 
The storage is primarily divided into three categories:
1. Core Server Binaries (~150 MB – 200 MB)
Base Install: A bare-bones installation typically uses around 110 MB to 175 MB.
Version Duplication: VS Code maintains separate binary sets for different versions of the client. If you use both VS Code and VS Code Insiders, or if you frequently update the client, multiple versions may be stored in ~/.vscode-server/bin, with each taking an additional ~150 MB. 
2. Extensions and Dependencies (Variable)
Extension Size: Most extensions are relatively small, but large ones (like C++ or Python support) can add 50 MB to 100+ MB each.
Workspace Data: Some language servers (e.g., C/C++) create significant indexing data and caches. By default, IntelliSense caching can occupy up to 5 GB unless manually limited in settings. 
3. Accumulated Growth and Caches
Usage-Based Growth: Over several months of active use, the ~/.vscode-server directory often reaches 1 GB to 2 GB.
Logs and Temp Files: Error logs and temporary runtime files can accumulate. In some reported cases, bug-related growth has caused the directory to exceed 6 GB before a manual cleanup was performed. 
Summary Table: Expected Storage Usage
Component 	Estimated Size
Bare Server Binary	110 – 175 MB
Typical Extensions (3-5)	200 – 400 MB
Active Caches/Indexes	100 MB – 5 GB (Configurable)
Total Typical Usage	500 MB – 2 GB
Tip: If space is limited, you can safely delete the ~/.vscode-server folder; the server and your extensions will automatically reinstall the next time you connect. 




How can I manage or limit storage usage by VS Code Server extensions and caches?

Tell me more about managing extension storage

How can I keep VS Code Server binaries updated?

undefined
undefined
undefined
8 sites
Copies of vscode-server installed on school servers take up ...
One other thing I'll note is that there is massive inefficiency in the replication of common code across all the different student...

GitHub

Is it a good idea to install Visual Studio Code Remote - SSH on a ...
Comments Section * Draziray. • 5y ago. Why are you doing development on a production server? Best practice would be to clone the p...

Reddit
The `.vscode-server/bin` directory is always growing in time ...
davetapley commented. davetapley. on Jul 16, 2024. Just ran in to this, 6GB since Feb 2022 🤯 I assume it's safe to just delete . ...

GitHub

Show all
## can vscode-server run in a no-exec directory?
January 03, 2026

No, VS Code Server cannot run directly in a directory mounted with the noexec option, as it must execute binaries (like Node.js) and shell scripts to function. Attempting to do so will result in "Permission denied" errors during the connection or installation process. 
To bypass this restriction without root permissions, use one of the following workarounds:
1. Configure a Custom Installation Path
The most direct fix is to tell VS Code to install its server in a different directory (e.g., /tmp or a different partition) that does not have the noexec restriction.
Open your local VS Code Settings (Ctrl+,).
Search for "Remote.SSH: Server Install Path".
Add an entry for your remote host with a path where execution is allowed (e.g., {"my-remote-host": "/tmp/.vscode-server"}).
2. Use a Symbolic Link
If you cannot change the setting, you can redirect the default installation folder to an executable location using a symlink: 
SSH into the remote server via a terminal.
Create a directory in an executable location: mkdir -p /path/to/exec/dir/.vscode-server.
Link it to your home directory: ln -s /path/to/exec/dir/.vscode-server ~/.vscode-server.
3. Move the Directory Manually
If the server is already installed but fails to run due to a recent noexec policy change:
Move the existing folder: mv ~/.vscode-server /path/to/exec/dir/.
Create the symlink as described in the step above. 
Why this happens
The noexec mount option is a security feature that prevents any file on that partition from being executed as a binary. Since VS Code Server relies on a bundled Node.js binary and several shell scripts located in ~/.vscode-server/bin, it will be blocked by this system-level setting even if the files have rwx permissions. 




What is the default location of the VS Code Server installation on remote Linux systems?

Tell me more about the symlink method for noexec

What is Node.js and why is it needed by VS Code Server?

undefined
undefined
undefined
4 sites
Remote SSH server permission denied during installation #3649
ibrahmd. on Sep 10, 2020 · edited by ibrahmd. Author. All the files have rwx permission, upon further checking, it turns out this ...

GitHub

Remote SSH Vscode plugin giving Permission Denied when ...
Install it from your system's package manager or https://code.visualstudio.com, restart your shell, and try again. If you already ...

Unix & Linux Stack Exchange
How to change vscode-server directory - visual-studio - Stack Overflow
Unfortunately there is no direct way yet with VSCode to install into a custom directory. You could follow below steps to move or i...

Stack Overflow

Show all
## can pycharm-helpers run in a no-exec directory?
January 03, 2026

No, PyCharm-helpers cannot run in a directory mounted with the noexec option. 
PyCharm-helpers consists of a suite of Python scripts and binaries used for debugging, profiling, and managing remote interpreters. Because the remote system must execute these scripts to provide IDE functionality, a noexec mount on the directory where they are stored will lead to "Permission denied" errors during script execution. 
Common Workarounds for 2026
If your home directory (default: ~/.pycharm_helpers) is on a noexec partition, you can use these methods to move the helpers: 
Modify jdk.table.xml (Recommended): Unlike VS Code, PyCharm does not have a direct setting in the UI to change the helper path. You must manually edit the configuration file on your local machine:
Locate jdk.table.xml in your PyCharm configuration directory (e.g., AppData\Roaming\JetBrains\PyCharm202x.x\options\ on Windows or Library/Application Support/JetBrains/PyCharm202x.x/options/ on macOS).
Find the <jdk> entry for your remote interpreter.
Change the path value within the HELPERS_PATH tag to an executable location on the remote server (e.g., /tmp/.pycharm_helpers).
Symbolic Link: You can redirect the default path to an executable partition:
On the remote server, create a folder in an executable directory: mkdir -p /exec/path/.pycharm_helpers.
Link it to your home directory: ln -s /exec/path/.pycharm_helpers ~/.pycharm_helpers.
Manual Mapping via SFTP: In some versions, you can attempt to map the helpers directory through the Deployment settings (Tools > Deployment > Configuration) by creating a mapping that points your local helpers to an executable remote path, though this is less reliable than editing jdk.table.xml. 
Why this is necessary
When PyCharm connects to a remote host, it uploads these helper files and then attempts to run them using the remote Python interpreter. If the underlying file system blocks execution via the noexec flag, the interpreter will fail to launch the helper modules, breaking features like the Debugger, Console, and Skeleton Generation. 