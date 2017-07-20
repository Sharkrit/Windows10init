# Windows10init
Initial setup basic Windows 10 components for Professional System Engineer

## Install Package Provider in PowerShell
`Get-PackageProvider -name NuGet,Chocolatey | Install-PackageProvider`

## Install Mandatory Package Provider for my Laptop
```
(New-Object Net.WebClient).DownloadString("http://psget.net/GetPsGet.ps1") | Invoke-Expression
(New-Object Net.WebClient).DownloadString('https://chocolatey.org/install.ps1') | Invoke-Expression
```
## Install standard and useful Apps
`Find-Package Git,7zip,notepadplusplus | Install-Package`

## Install InvokePsExec module
for execute PowerShell and batch/cmd.exe code on target Windows computers, using PsExec.exe (http://www.powershelladmin.com/wiki/Invoke-PsExec_for_PowerShell)
```
Install-Module -ModuleUrl https://www.powershellgallery.com/api/v2/package/InvokePsExec/ -Verbose
```
## Install HPE server management modules
```
Find-Package -Name HPERedfishCmdlets,HPRESTCmdlets,HPWarranty -Verbose | Install-Package -Verbose
choco install  -y -force hp-ilo-cmdlets hp-bios-cmdlets
````

## Install Remote Server Administration Tools (RSAT) for Windows 10
```
(New-Object Net.WebClient).DownloadString("https://raw.githubusercontent.com/kilasuit/PoshFunctions/Dev/Scripts/Install-Win10RSATTools.ps1") | Invoke-Expression
```

## Install VS Code (Free)
```
MkDir 'C:\Downloads'
Invoke-WebRequest 'https://go.microsoft.com/fwlink/?LinkID=623230' -OutFile 'C:\Downloads\VsCodeSetup.exe' -Verbose | iex 'C:\Downloads\VsCodeSetup.exe /Silent /NoRestart /NoCloseApplications /NoRestartApplications' -Verbose
```

## Enabled Windows Developer Mode to get WSL
`Get-WindowsDeveloperLicense`
`Show-WindowsDeveloperLicenseRegistration` # Please click on radio button "Developer Mode"

## Enable Optional Features for Hyper-V, Container, and Bash on Windows.
```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All,containers,Microsoft-Windows-Subsystem-Linux -All
Restart-Computer -Force
```

## After computer restarted, you can run this command for automatically Bash installation and set the default user to “root” with no password
`lxrun /install /y`

## Install Docker Community Edition on Windows 10 for support Windows Container
`Invoke-WebRequest -UseBasicParsing -Uri 'https://download.docker.com/win/stable/InstallDocker.msi' -OutFile $($Env:TMP + '\InstallDocker.msi')`

`Msiexec /i $($Env:TMP + '\InstallDocker.msi')`

`Restart-Computer`

- Check version of Docker and OS Architecture "OS/Arch" must be "windows/amd64"

`docker version`

```Client:
 Version:      17.06.0-ce
 API version:  1.30
 Go version:   go1.8.3
 Git commit:   02c1d87
 Built:        Fri Jun 23 21:30:30 2017
 OS/Arch:      windows/amd64

Server:
 Version:      17.06.0-ce
 API version:  1.30 (minimum version 1.24)
 Go version:   go1.8.3
 Git commit:   02c1d87
 Built:        Fri Jun 23 22:19:00 2017
 OS/Arch:      windows/amd64
 Experimental: true
```

## Testing your Windows Container

`docker run microsoft/dotnet-samples:dotnetapp-nanoserver`
```
        Dotnet-bot: Welcome to using .NET Core!
    __________________
                      \
                       \
                          ....
                          ....'
                           ....
                        ..........
                    .............'..'..
                 ................'..'.....
               .......'..........'..'..'....
              ........'..........'..'..'.....
             .'....'..'..........'..'.......'.
             .'..................'...   ......
             .  ......'.........         .....
             .                           ......
            ..    .            ..        ......
           ....       .                 .......
           ......  .......          ............
            ................  ......................
            ........................'................
           ......................'..'......    .......
        .........................'..'.....       .......
     ........    ..'.............'..'....      ..........
   ..'..'...      ...............'.......      ..........
  ...'......     ...... ..........  ......         .......
 ...........   .......              ........        ......
.......        '...'.'.              '.'.'.'         ....
.......       .....'..               ..'.....
   ..       ..........               ..'........
          ............               ..............
         .............               '..............
        ...........'..              .'.'............
       ...............              .'.'.............
      .............'..               ..'..'...........
      ...............                 .'..............
       .........                        ..............
        .....
**Environment**
Platform: .NET Core 1.0
OS: Microsoft Windows 10.0.14393
```
- TIPS: If enabled BitLocker with "Deny write access to fixed drives not protected by BitLocker" and this error message
`failed to register layer: re-exec error: exit status 1: output: ProcessBaseLayer C:\ProgramData\Docker The I/O operation has been aborted because of either a thread exit or an application request.`

- You need to run to PowerShell command

`Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Policies\Microsoft\FVE" -Name FDVDenyWriteAccess -Value 0`

- TIPS: Docker deamon cannot start run pulled images, with error message;

`re-exec error: exit status 1: output: ProcessBaseLayer C:\ProgramData\Docker\windowsfilter The system cannot find the path specified.`
- You need to run to PowerShell command

`Set-ItemProperty -Path 'HKLM:SOFTWARE\Microsoft\Windows NT\CurrentVersion\Virtualization\Containers' -Name VSmbDisableOplocks -Type DWord -Value 1 -Force`
## TroubleShooting Windows Contrainer Host
You can simply run a PowerShell script from Microsoft on your Windows 10 for diagnoistic.

`PS C:\windows\system32> Invoke-WebRequest https://aka.ms/Debug-ContainerHost.ps1 -UseBasicParsing | Invoke-Expression`
```
Checking for common problems
Describing Windows Version and Prerequisites
 [+] Is Windows 10 Anniversary Update or Windows Server 2016 617ms
 [+] Has KB3192366, KB3194496, or later installed if running Windows build 14393 111ms
 [+] Is not a build with blocking issues 22ms
 [+] Has 'Containers' feature installed 11.29s
Describing Docker is installed
 [+] A Docker service is installed - 'Docker' or 'com.Docker.Service'  69ms
 [+] Service is running 22ms
 [+] Docker.exe is in path 2.78s
 [+] Docker is registered in the EventLog service 103ms
Describing User has permissions to use Docker daemon
 [+] docker.exe should not return access denied 39ms
Describing Windows container settings are correct
 [+] Do not have DisableVSmbOplock set to 1 29ms
 [+] Do not have zz values set 25ms
 [+] Do not have FDVDenyWriteAccess set to 1 27ms
Describing The right container base images are installed
 [+] At least one of 'microsoft/windowsservercore' or 'microsoft/nanoserver' should be installed 107ms
Describing Container network is created
 [+] At least one local container network is available 3.33s
 [+] At least one NAT, Transparent, or L2Bridge Network exists 29ms
 [+] NAT Network's vSwitch is internal 67ms
 [+] A Windows NAT is configured if a Docker NAT network exists 299ms
```
