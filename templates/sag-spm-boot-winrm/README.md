# Bootstrapping SPM on remote Windows machines

## Requirements

### System requirements for the target Windows machines

* Windows 2007 or later
* Powershell version 5.0 or higher
* DotNet 4.5 or higher ( used for unzip )
* The connecting user account must have Administrator privileges
* WinRM service must be enabled
* Memory used by powershell should be at 2GB limit or more

Run these commands from the powershell window as Administrator to ensure the last two requirements:

```powershell
PS> Enable-PSRemoting -SkipNetworkProfileCheck
PS> Set-Item WSMan:\localhost\Shell\MaxMemoryPerShellMB 2048
```

### Requirements for Command Central machine

* Windows 2007 of later
* Powershell version 5.0 or higher
* DotNet 4.5 or higher ( used for unzip )
* Must have Command Central bootstrap installer for Windows (.zip) saved in `CC_HOME\profiles\CCE\data\installers` folder

## Running as a standalone Composite Template

Bootstrap remote Windows machines on host1 and host2 with 10.2 version of SPM into C:\SoftwareAG2
installation directory on port 8292. The remote connection is done using sagadmin user account that
has administrative privileges on host1 and host2. List of hosts should be given as yaml list i.e. ["host1","host2"]

```bash
sagcc exec templates composite apply sag-spm-boot-winrm nodes=["host1"],["host2"] \
  cc.installer=cc-def-10.2-milestone-w64.zip \
  install.dir=C:\\SoftwareaAG2 \
  spm.port=8292 \
  os.username=sagadmin os.password=**** \
  skip.runtime.validation=true
```

> NOTE: skip.runtime.validation=true is to avoid SSH validation error

## Adding Windows Infrastructure layer to a Stack

TBD

```bash
```

Known problems:

* SSH validation must be skipped
* `os.credentials.key` cannot be used

## Using from Stacks UI

Not supported yet

Known problems:

* SSH validation must be skipped
* `cc.installer` variable is not recognized unless it is in the bootstrapInfo section which is not required
* `action` parameter shows in the UI but it's not a parameter
