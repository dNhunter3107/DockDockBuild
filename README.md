DockDockBuild: Docker Based Makefile Support for IntelliJ-Based IDEs
====================================================================

DockDockBuild is an open source plugin for IntelliJ that dockerizes your makefile compilation.

![logo](https://github.com/intuit/DockDockBuild/blob/master/images/logo_banner.png?raw=true)

[![Build Status](https://travis-ci.com/intuit/DockDockBuild.svg?branch=master)](https://travis-ci.com/intuit/DockDockBuild)
[![codecov](https://codecov.io/gh/intuit/DockDockBuild/branch/master/graph/badge.svg)](https://codecov.io/gh/intuit/DockDockBuild)
[![GitHub release](https://img.shields.io/github/release/intuit/DockDockBuild.svg)](https://github.com/intuit/DockDockBuild/releases)


When compiling a target in a makefile, this plugin builds a docker container and runs the compilation in that container. 
The container shares the host's code root path by using *Docker's volume mapping*, making the compiled code available and persistent. 
  
DockDockBuild allows developers to compile their project on an immutable, uniform environment, 
without dedicated installations on their machine - just docker.

This plugin fully supports GNU Make syntax, and provides:

 * Syntax highlighting
 * Keyword & target name completion
 * Run configurations
 * Gutter marks & context actions to run targets
 * Quick-doc for ## comments on target line
 * *Find Usages* and *Go To Symbol* navigation for targets
 * Prerequisites resolution
 * Rules, variables and conditionals folding
 * Quick fixes to create new rule from unresolved prerequisite and remove empty rule
  
Based on https://plugins.jetbrains.com/plugin/9333-makefile-support

Prerequisites
-------------
  1. Docker - see installation instructions for [Mac][Mac], [Windows][Windows] and [Ubuntu][Ubuntu].
      >Please note that in some cases, it is recommended that Mac and Windows users install [Docker Toolbox][Docker Toolbox] rather than Docker Desktop, as the latter might cause compatibility issues with Hyper-V - read more [here](https://docs.docker.com/docker-for-mac/docker-toolbox/).
  		
  2. IntelliJ 2019.01 and above - if you have an older version, download the latest one [here][intelliJ download]. It is recommended to uninstall the old version silently, so that you don't lose your old configuration.
  
Installation
------------
### From the IntelliJ Plugin Marketplace
In Settings, click on Plugins. You will find DockDockBuild in the Marketplace tab. 
Click on **Install**. 

###### You may be required to restart your IDE following the installation.
### From zip
In your IntelliJ Settings screen, click on **Plugins**. Click on **Settings** and select **Install Plugin From Disk**:
 
![Install plugin from zip](https://github.com/intuit/DockDockBuild/blob/master/images/install_from_zip.png?raw=true)

Select the zip file, and then click on **Install**. 

###### You may be required to restart your IDE following the installation.


Configuration
-------------
### Plugin Configuration
Since DockDockBuild builds a docker container and runs the make command on it, you need to define the paths to your Docker executable, code root and Maven cache, for example:

Docker Desktop for Mac:
![plugin_config](https://github.com/intuit/DockDockBuild/blob/master/images/plugin_config.png?raw=true)

Set the **Path to Docker executable** to be the location of the *docker.exe* file on your machine.

Set the **Path to code root** to be the location of your project's source code root folder.

Set the **Path to Maven cache** as the location of your .m2 folder.

Optionally: Set the **Advanced docker settings** with additional settings for the docker run command (mount additional volumes, etc.).

### Run Configuration
DockDockBuild's **run configuration** is where you can select which Makefile and Dockerfile are used for building your project, 
as well as define environment variables. 

When compiling a specific target from your Makefile, a default Run Configuration is set and used 
instantly by DockDockBuild, based on the location of the Makefile and the target being compiled.

You may also define multiple run configurations manually, and select which one to use each
time you compile a target.

![run config.jpg](https://github.com/intuit/DockDockBuild/blob/master/images/run_config.png?raw=true)


* Name your configuration.
* Select the local Dockerfile or Docker image you would like to use.
* Select the Makefile you would like to run.
* Specify the target to be built.
* [OPTIONAL] Select a script to be run on the container before the *make* command, that sets environment variables, 
runs installations etc.  

### Additional Configuration for Docker-Toolbox Users
If you are using Docker Toolbox, you need to set up your Virtual Machine to enable folder sharing between your host machine
and the VM.
In order to compile your target on a container, your code root folder needs to be shared with the container. 
Since your container is running using the VirtualBox hypervisor, you need to allow your VM access to the relevant folder:
1. Make sure Docker Toolbox is not running, by running the `docker-machine stop` command in your terminal.
2. Open VirtualBox.
3. Right-click on the machine that is used by Docker-Toolbox. It is usually named "default", unless you have changed it 
manually.
4. Click on "Settings", and go to the Shared Folders tab: ![shared folders screen.jpg](https://github.com/intuit/DockDockBuild/blob/master/images/vm_shared_folders_screen.JPG?raw=true)
Click on the **+** button.
5. Select your project's code root folder, and **give it the same name as defined in your DockDockBuild run configuration as code root**.
  > For Windows users:
 Please note that the path configuration cannot include any special characters. For example,
To define the path *C:/Users/Me/MyProject*, the configuration should be */c/Users/Me/MyProject*.
6. Start Docker Toolbox by running the command `docker-machine start` in your terminal.
7. Connect to your docker-machine by SSH: `docker-machine ssh`.
8. Create a folder as a mount point for your volume, for example - `mkdir mount_here`.
9. Mount the shared folder, which you have defined in VirtualBox, at the folder you have created - 
`sudo mount -t vboxsf -o uid=1000,gid=50 MyProject /home/docker/mount_here`.

Your docker machine now has access to your project's source code root. 

Usage
-----
There are multiple ways to build your project using DockDockBuild:

1. Select a run configuration from the drop-down list and click on the "play" button. ![select_run_config.png](https://github.com/intuit//DockDockBuild/blob/master/images/select_run_config.png?raw=true)
2. Go to your Makefile, and click on the "play" button next to the relevant target.

Development
--------------------------------------------

The plugin is built using Gradle and uses [gradle-intellij-plugin][gradle-intellij-plugin] to integrate with IntelliJ Platform.

First, make sure gradle is using java 8, and not any other version.

To build a plugin run

```
$ ./gradlew buildPlugin
```

Plugin zip file will be created in `build/distributions`

To build & test the plugin in IDE run `./gradlew runIdea`

### Grammar modifications

The plugin uses [Grammar-Kit][Grammar-Kit] to generate parser and lexer. Please install [Grammar-Kit plugin][Grammar-Kit plugin] and refer to the documentation if you want to modify grammar.

To regenerate parser, open Makefile.bnf and press Ctrl+Shift+G (Cmd+Shift+G on Mac)
To regenerate lexer, open Makefile.flex and press Ctrl+Shift+G (Cmd+Shift+G on Mac)

Please make sure to add test to MakefileParserTest.kt for any parser modifications.
 
Contribution
------------
We welcome contributions from everyone. Please refer to the documentation [here](.github/CONTRIBUTING.md) for more info.

[Mac]:https://docs.docker.com/docker-for-mac/install/
[Windows]:https://docs.docker.com/docker-for-windows/install/
[Ubuntu]:https://docs.docker.com/install/linux/docker-ee/ubuntu/
[Docker Toolbox]:https://docs.docker.com/toolbox/overview/

[intelliJ download]:https://www.jetbrains.com/idea/download
[gradle-intellij-plugin]:https://github.com/JetBrains/gradle-intellij-plugin
[Grammar-Kit]:https://github.com/jetbrains/grammar-kit
[Grammar-Kit plugin]:https://plugins.jetbrains.com/plugin/6606-grammar-kit