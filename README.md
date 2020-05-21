# WSL ArchLinux

## Table of Contents
1. [Introduction](#introduction)
2. [Requirements](#requirements)
3. [Installation](#installation)
4. [Build and Test](#build-and-test)
5. [Issues](#issues)
6. [Contributing](#contributing)

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
- Enable Windows Subsystem for Linux in Windows Features and Developer Mode in the Settings app.

### For building

- You must have `install.tar.gz` in your root of the project. Download [latest release](https://github.com/bilguun0203/ArchLinuxFS/releases/latest) from [bilguun0203/ArchLinuxFS](https://github.com/bilguun0203/ArchLinuxFS).

## Installation

0. Check [requirements](#requirements)
1. Download latest `ArchLinux-Appx_1.0.x.0_x64.zip` from releases.
2. Extract zip file and run `Add-AppDevPackage.ps1` with powershell
3. Accept all
4. When finished select Arch Linux from Start menu or type `arch` on cmd to start
5. After Installation finished you should enter your username (it can be different from your windows username).
6. Before use pacman, initialize pacman keyring with following two commands
```
$ sudo pacman-key --init
$ sudo pacman-key --populate archlinux
```
or if you changed your user to root, use this
```
# pacman-key --init
# pacman-key --populate archlinux
```

## Build and Test
  _This section from Microsoft/WSL-DistroLauncher_
  
  To help building and testing the DistroLauncher project, we've included the following scripts to automate some tasks. You can either choose to use these scripts from the command line, or work directly in Visual Studio, whatever your preference is. 

So, if I wanted to instead call my distro "TheBestDistroEver", I'd change this to:
``` xml
<PropertyGroup Label="Globals">
  ...
  <ProjectName>TheBestDistroEver</ProjectName>
</PropertyGroup>
```

> Note: **DO NOT** change the ProjectName of the `DistroLauncher/DistroLauncher.vcxproj` from the value `launcher`. Doing so will break the build, as the DistroLauncher-Appx project is looking for the output of this project as `launcher.exe`.

6.  Update `MyDistro.appxmanifest`. There are several properties that are in the manifest that will need to be updated with your specific values:
    1. Note the `Identity Publisher` value (by default, `"CN=DistroOwner"`). We'll need that for testing the application.
    1. Ensure `<desktop:ExecutionAlias Alias="mydistro.exe" />` ends in ".exe". This is the command that will be used to launch your distro from the command line and should match the executable name we picked in step 4.
    1. Make sure each of the `Executable` values matches the executable name we picked in step 4.

7. Copy your tar.gz containing your distro into the root of the project and rename it to `install.tar.gz`.

## Setting up your Windows Environment
You will need a Windows environment to test that your app installs and works as expected. To set up a Windows environment for testing you can follow the steps from the [Windows Dev Center](https://developer.microsoft.com/en-us/windows/downloads/virtual-machines).

> Note: If you are using Hyper-V you can use the new VM gallery to easily spin up a Windows instance.

Also, to allow your locally built distro package to be manually side-loaded, ensure you've enabled Developer Mode in the Settings app (sideloading won't work without it). 

## Build and Test

To help building and testing the DistroLauncher project, we've included several scripts to automate some tasks. You can either choose to use these scripts from the command line, or work directly in Visual Studio, whatever your preference is. 

> **Note**: some sideloading/deployment steps don't work if you mix and match Visual Studio and the command line for development. If you run into errors while trying to deploy your app after already deploying it once, the easiest step is usually just to uninstall the previously sideloaded version and try again. 

### Building the Project (Command line):
To compile the project, you can simply type `build` in the root of the project to use MSBuild to build the solution. This is useful for verifying that your application compiles. It will also build an appx for you to sideload on your dev machine for testing.

> Note: We recommend that you build your launcher from the "Developer Command Prompt for Visual Studio" which can be launched from the start menu. This command-prompt sets up several path and environment variables to make building easier and smoother.

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

### Installing & Testing
You should now have a finished appx sideloaded on your machine for testing.

To install your distro package, double click on the signed appx and click "Install". Note that this only installs the appx on your system - it doesn't unzip the tar.gz or register the distro yet. 

You should now find your distro in the Start menu, and you can launch your distro by clicking its Start menu tile or executing your distro from the command line by entering its name into a Cmd/PowerShell Console.

When you first run your newly installed distro, it is unpacked and registered with WSL. This can take a couple of minutes while all your distro files are unpacked and copied to your drive. 

Once complete, you should see a Console window with your distro running inside it.

### Publishing
If you are a distro vendor and want to publish  your distro to the Windows store, you will need to complete some pre-requisite steps to ensure the quality and integrity of the WSL distro ecosystem, and to safeguard our users:

#### Publishing Pre-Requisites
1. Reach out to the WSL team to introduce your distro, yourself, and your team
1. Agree with the WSL team on a testing and publishing plan
1. Complete any required paperwork
1. Sign up for an "Company" Windows Developer Account https://developer.microsoft.com/en-us/store/register. 
    > Note: This can take a week or more since you'll be required to confirm your organization's identity with an independent verification service via email and/or telephone.

#### Publishing Code changes
You'll also need to change a few small things in your project to prepare your distro for publishing to the Windows store

1. In your appxmanifest, you will need to change the values of the Identity field to match your identity in your Windows Store account:

``` xml
<Identity Name="1234YourCompanyName.YourAppName"
          Version="1.0.1.0"
          Publisher="CN=12345678-045C-ABCD-1234-ABCDEF987654"
          ProcessorArchitecture="x64" />
```

  > **NOTE**: Visual Studio can update this for you! You can do that by right-clicking on "DistroLauncher-Appx (Universal Windows)" in the solution explorer and clicking on "Store... Associate App with the Store..." and following the wizard. 

## Issues
Any bugs or problems discovered with the Launcher should be filed in this project's Issues list. If you discovered bugs or problems with ArchLinuxFS, that should be filed in [bilguun0203/ArchLinuxFS](https://github.com/bilguun0203/ArchLinuxFS)'s Issues list.

## Contributing
Your are free to contribute.
