# Bootstrapping SPM on remote Windows machines

## Requirements

### System requirements for the target Windows machines

* Windows 2007 or later
* DotNet 4.5 or higher (prerequisite for recent powershell versions). Follow the official documentation for installing or upgrading dotNet Framework here: https://www.microsoft.com/net/download/windows
* Powershell version 5.0 or higher. 
  Follow the official documentation for upgrading to powershell 5.1 for relevant operating system version and platform here https://www.microsoft.com/en-us/download/details.aspx?id=54616 (have in mind that this requires restart)

* The connecting user account must have Administrator privileges
* WinRM service must be enabled
* Memory used by powershell should be at 2GB limit or more

Run these commands from the powershell window as Administrator to meet the last two requirements:

```powershell
PS> Enable-PSRemoting -SkipNetworkProfileCheck
PS> Set-Item WSMan:\localhost\Shell\MaxMemoryPerShellMB 2048
```
NOTE: Although it is not recommended to keep WinRM enabled when in production, here are the steps to enable it at boot time:
* Open Services tool (Win+R -> services.msc), locate the service "Windows Remote Management (WS-Management)", go to the service properties and change the start type to "Autostart"
* Open Group Policy Editor (Win+R -> gpedit.msc) , go to Computer Configuration -> Administrative Templates -> Windows Components -> Windows Remote Services -> WinRM Service, open "Allow remote server management" through WinRM, click "enabled," and add `*` to the IPv4 filter.



### Requirements for Command Central machine

* Windows 2007 or later
* DotNet 4.5 or higher (prerequisite for recent powershell versions). Follow the official documenation for installing or upgrading dotNet Framework here:https://www.microsoft.com/net/download/windows
* Powershell version 6 or later.
  Follow the official documenation for installing powershell 6 for relevant operating system version and platform here https://github.com/PowerShell/PowerShell . For Windows 7 install "Windows 10 Universal C Runtime" might be necessary.
* Script execution policy should be set to unrestricted
```powershell
PS> Set-ExecutionPolicy -ExecutionPolicy Unrestricted
```
* Must have Command Central bootstrap installer for Windows (.zip) saved in `CC_HOME\profiles\CCE\data\installers` folder. Verify by running:

```bash
sagcc list provisioning bootstrap installers platform=w64
```

## Running as a standalone Composite Template

Bootstrap remote Windows machines on host1 and host2 with 10.2 version of SPM into C:\SoftwareAG2
installation directory on port 8292. The remote connection is done using `sagadmin` user account that
has administrative privileges on host1 and host2. List of hosts should be given as yaml list i.e. ["host1","host2"]

```bash
sagcc exec templates composite apply sag-spm-boot-winrm nodes=["host1","host2"] \
  cc.installer=cc-def-10.2-milestone-w64.zip \
  install.dir=C:\SoftwareaAG2 \
  spm.port=8292 \
  os.username=sagadmin os.password=**** \
  ```

> NOTE: skip.runtime.validation=true is to avoid SSH validation error

## Adding Windows Infrastructure layer to a Stack

Create a new 10.2 DevWin01 stack and provision Windows infrastructure layer onto host1 and host2:

```bash
sagcc create stacks alias=DevWin01 release=10.2
sagcc create stacks DevWin01 layers alias=WinInfra layerType=INFRA-REMOTE-WINDOWS nodes=host1,host2 \
  cc.installer=cc-def-10.2-milestone-w64.zip \
  install.dir=C:\\SoftwareaAG2 \
  spm.port=8292 \
  os.username=sagadmin os.password=**** \
```


## Using from Stacks UI

* Requires Command Central 10.2


## Local Debuging from Mac

```bash
pwsh -f templates/sag-spm-boot-winrm/push-bootstrap.ps1 -Computername \["bgninjabvt01"\] -RemoteTempPath C:\Windows\Temp -LocalInstallerZip ~/sag/cc/profiles/CCE/data/installers/cc-def-10.2-milestone-w64.zip -RemoteInstallPath C:\sag2 -AcceptLicense -PlainCredentials vmtest:vmtest
```
