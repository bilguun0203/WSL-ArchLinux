# WSL ArchLinux
## Introduction 
  This is clone of [WSL-DistroLauncher](https://github.com/Microsoft/WSL-DistroLauncher) and modified to use Arch Linux as a Windows Subsystem for Linux (WSL). You can use this project to create Arch Linux application that can be submitted to the Microsoft Store or sideloaded on your dev machine. Or if you don't want to build it yourself, you can download created package (See [installation](#installation)).

  Read more about WSL-DistroLauncher: [WSL-DistroLauncher](https://github.com/Microsoft/WSL-DistroLauncher).
  
  Note: This project is written in C++.
  
### Contents
  This provides the following functionality:

  * `arch`
    - Launch default login shell.

  * `arch run <command line>`
    - Run the given command line in that distro, using the default configuration.
    - Everything after `run ` is passed to the Linux LaunchProcess call.

  * `arch config [setting [value]]`
    - Configure certain settings for this distro.
    - Settings are any of the following (by default)
      - `--default-user <username>`: Set the default user for this distro

  * `arch help`
    - Print the usage.

## Requirements

### For running and building

- Windows build 16215 or later
- Enabled Windows Subsystem for Linux in Windows Features and Developer Mode in the Settings app.

### For building

- You must have `install.tar.gz` in your root of the project. Download latest release from [bilguun0203/ArchLinuxFS](https://github.com/bilguun0203/ArchLinuxFS/releases/latest).
- 

## Installation

0. Check [requirements](#requirements)
1. Download latest `ArchLinux-Appx_1.0.x.0.zip` from releases.
2. Extract zip file and run `Add-AppDevPackage.ps1` with powershell
3. Accept all
4. When finished select Arch Linux from Start or type `arch` on cmd to start
5. After Installation finished you should enter your username (it can be different from your windows username).
6. Before use pacman, initialize pacman keyring with following two commands
```
$ sudo pacman-key --init
$ sudo pacman-key --populate archlinux
```
or if you changed your user to root, use this
```
$ sudo pacman-key --init
$ sudo pacman-key --populate archlinux
```

## Build and Test
  _This section from Microsoft/WSL-DistroLauncher_
  
  To help building and testing the DistroLauncher project, we've included the following scripts to automate some tasks. You can either choose to use these scripts from the command line, or work directly in Visual Studio, whatever your preference is. 

  **Please Note** some sideloading/deployment steps don't work if you mix and match Visual Studio and the command line for development. If you run into errors while trying to deploy your app after already deploying it once, the easiest step is usually just to uninstall the previously sideloaded version and try again. 

### Setting up your Windows Environment
You will need a Windows environment to test that your app installs and works as expected. To set up a Windows environment for testing you can follow the steps from the [Windows Dev Center](https://developer.microsoft.com/en-us/windows/downloads/virtual-machines).

Note: If you are using Hyper-V you can use the new VM gallery to easily spin up a Windows instance.

### Building Project (Command line):
  To compile the project, you can simply type `build` in the root of the project
  to use MSBuild to build the solution. This is useful for verifying that your application compiles. It will also build an appx for you to sideload on your dev machine for testing.
  
  `build.bat` assumes that MSBuild is installed at one of the following paths:
  `%ProgramFiles*%\MSBuild\14.0\bin\msbuild.exe` or
  `%ProgramFiles*%\Microsoft Visual Studio\2017\Enterprise\MSBuild\15.0\Bin\msbuild.exe` or
  `%ProgramFiles*%\Microsoft Visual Studio\2017\Community\MSBuild\15.0\Bin\msbuild.exe`.

  If that's not the case, then you will need to modify that script.

  Once you've completed the build, the packaged appx should be placed in a directory like `WSL-DistroLauncher\x64\Release\DistroLauncher-Appx` and should be named something like `DistroLauncher-Appx_1.0.0.0_x64.appx`. Simply double click that appx file to open the sideloading dialog. 

  You can also use the PowerShell cmdlet `Add-AppxPackage` to register your appx:
  ``` powershell
  powershell Add-AppxPackage x64\Debug\DistroLauncher-Appx\DistroLauncher-Appx_1.0.0.0_x64_Debug.appx
  ```

### Building Project (Visual Studio):

  You can also easily build and deploy the distro launcher from Visual Studio. To sideload your appx on your machine for testing, all you need to do is right-click on the "Solution (DistroLauncher)" in the Solution Explorer and click "Deploy Solution". This should build the project and sideload it automatically for testing.

  In order run your solution under the Visual Studio debugger, you will need to copy your install.tar.gz file into your output folder, for example: `x64\Debug`. **NOTE: If you have registered your distribution by this method, you will need to manually unregister it via wslconfig.exe /unregister**

### Testing
  You should now have a finished appx sideloaded on your machine for testing.

  But before you do that, make sure you've enabled Developer Mode in the Settings app (sideloading won't work without it). Then double click on the signed appx and click "Install" to install it. Note that this only installed the appx on your system, but it doesn't unzip the tar.gz or register the distro yet. 

  You can then begin the distro registration by launching the app from the Start Menu or executing `mydistro` from the command line. 

### Publishing
  Once you are ready to upload your appx to the store, you will need to change a few small things to prepare the appx for the store.  

  1. In the appxmanifest, you will need to change the values of the Identity to match the value given to you by the store. This should look like the following:

  ``` xml
  <Identity Name="1234YourCompanyName.YourAppName"
            Version="1.0.1.0"
            Publisher="CN=12345678-045C-ABCD-1234-ABCDEF987654"
            ProcessorArchitecture="x64" />
  ```

  **NOTE**: Visual Studio can update this for you. You can do that by right-clicking on "DistroLauncher-Appx (Universal Windows)" in the solution explorer and clicking on "Store... Associate App with the Store..." and following the wizard. 

  2. You will either need to run `build rel` from the command line to generate the Release version of your appx or use Visual Studio directly to upload your package to the store. You can do this by right-clicking on "DistroLauncher-Appx (Universal Windows)" in the solution explorer and clicking on "Store... Create App Packages..." and following the wizard. 

  Also make sure to check out the [Notes for uploading to the Store](https://github.com/Microsoft/WSL-DistroLauncher/wiki/Notes-for-uploading-to-the-Store) page on our wiki for more information.

# Issues
Any bugs or problems discovered with the Launcher should be filed in this project's Issues list. If you discovered bugs or problems with ArchLinuxFS, that should be filed in [bilguun0203/ArchLinuxFS](https://github.com/bilguun0203/ArchLinuxFS)'s Issues list.

# Contributing
Your are free to contribute.
