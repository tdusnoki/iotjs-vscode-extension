# IoTjs VSCode Extension Quickstart Guide
This guide is dedicated to Linux users.

- [Install VSCode](#install-vscode)
- [Build IoT.js](#build-iotjs)
- [Install extension](#install-extension)
- [Configure launch.json](#configure-launch.json)
- [Debug using Attach](#debug-using-attach)
- [Start debug server](#start-debug-server)
- [Debug using Launch](#debug-using-launch)
- [Install Tizen Studio](#install-tizen-studio)
- [Create Native Tizen Project](#create-and-install-native-tizen-project)
- [Package and Install Tizen Project to Tizen devices](#package-and-install-tizen-project-to-tizen-devices)

### Install VSCode
First you need to download and install VSCode (short for Visual Studio Code). You can download it from [HERE.](https://code.visualstudio.com/Download)
After downloading the .deb file (for Linux) you need to install it via a graphical command center if it's available or command line with the following command:
```sh
$ sudo dpkg -i <file>.deb
```
and to install dependencies:
```sh 
$ sudo apt-get install -f
```
### Build IoT.js
After successfully installing VSCode you need [IoT.js](https://github.com/Samsung/iotjs) in order to debug your code.
You need to clone it's git repository to your device:
```sh
$ git clone https://github.com/Samsung/iotjs.git
```
After that you need to build IoT.js:
```sh
$ cd iotjs
$ ./tools/build.py --buildtype=debug --jerry-debugger
```
After the build successfully finished you will have the executable in `/build/x86_64-linux/debug/bin/`.
The absolute path to this `iotjs` executable will be needed later on.

### Install extension

Now you can open a project with VSCode. All you need to do is open a terminal and navigate to your project directory then open the project with the following command:
```sh
$ cd /path/to/project
$ code .
```
To use the extension you need to install it first in VSCode. You can do this by clicking on the Extensions icon in the Activity Bar on the left or pressing `CTRL + SHIFT + X`.

![icon](https://code.visualstudio.com/assets/docs/editor/extension-gallery/extensions-view-icon.png)

Search for **iotjs** in the search bar and click on install and after a successful install, you will see a **Reload** button. Clicking on it will restart VSCode to enable the new extension.

### Configure launch.json

Now that the extension is active you need to create a launch.json file for your project.
In order to do this you will need a **.vscode** folder inside your project directory.
In your project root directory:
```sh
$ mkdir .vscode
$ touch .vscode/launch.json
```
After creating this file you can open it in VSCode and copy these lines to it:
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "IoT.js: Attach",
            "type": "iotjs",
            "request": "attach",
            "address": "localhost",
            "port": 5001,
            "localRoot": "${workspaceRoot}",
            "stopOnEntry": false,
            "debugLog": 4
        },
        {
            "name": "IoT.js: Launch",
            "type": "iotjs",
            "request": "launch",
            "address": "localhost",
            "program": "iotjs",
            "debugLog": 4,
            "stopOnEntry": false,
            "localRoot": "${workspaceRoot}",
            "port": 5001,
            "args": [
                "--start-debug-server",
                "--debugger-wait-source"
            ]
        }
    ]
}
```
There are two kinds of debug configurations: **attach** and **launch**.
The attach configuration is used when you are attaching to a debug server which is started separately.
These options are required for **attach**:
- `name`: The name which will be visible in the debug view
- `type`: This must be `iotjs` otherwise the debug session wont start
- `request`: Type of the session start
- `address`: IP address on which the server listening. Default is `localhost`
- `port`: Debug port to attach to. Default is `5001`
- `localRoot`: The local source root directoy, most cases this is the `${workspaceRoot}`
- `stopOnEntry`: Automatically stop the program after launch, the IoT.js will stop on the first breakpoint for now, no matter that is enabled or not.
- `debugLog`: The type of the debug log, you can choose from 0 to 4:
    - 0: none
    - 1: Error (show errors only)
    - 2: Debug Session related (requests and their responses)
    - 3: Debug Protocol related (communication between the engine and the client)
    - 4: Verbose (each log type included)

The launch configuration is used when you want to automatically start a debug server.
These options are required for **launch**:
- `name`: The name which will be visible in the debug view
- `type`: This must be `iotjs` otherwise the debug session wont start
- `request`: Type of the session start
- `program`: Runtime executable for debug server. Default is iotjs. If you debug on desktop use
absolute path to executable (e.g.:/path/to/iotjs/build/x86_64-linux/debug/bin/iotjs)
- `address`: IP address on which the server listening. Default is `localhost`
- `port`: Debug port to attach to. Default is `5001`
- `localRoot`: The local source root directoy, most cases this is the `${workspaceRoot}`
- `stopOnEntry`: Automatically stop the program after launch, the IoT.js will stop on the first breakpoint for now, no matter that is enabled or not.
- `debugLog`: The type of the debug log, you can choose from 0 to 4:
    - 0: none
    - 1: Error (show errors only)
    - 2: Debug Session related (requests and their responses)
    - 3: Debug Protocol related (communication between the engine and the client)
    - 4: Verbose (each log type included)
- `args`: Arguments for debug server. In case of IoT.js use --start-debug-server and --debugger-wait-source.

### Debug using Attach

Now you are almost ready to debug your code in VSCode. You only need to choose how do you want to start the debug server. 
In VSCode click on the **debug** icon on the Activity bar on the left or press `CTRL + SHIFT + D`.

![icon](https://code.visualstudio.com/assets/docs/python/debugging/debug-icon.png)

If you want to attach to an [already running debug server](#start-debug-server), choose **IoT.js: Attach** as your configuration:
![configicon](/images/iotjs-attach.png)

Now if you press `F5` or click on the green triangle next to the debug configuration dropdown menu, your debug session will start and a dialog window will pop up asking for a file to debug.

### Start debug server

To start a debug server, first navigate to your IoT.js directory. Inside your directory you should be able to find the executable for IoT.js( if you have built it) in `/build/x86_64-linux/debug/bin/iotjs`. Enter this directory:
```sh
$ cd build/x86_64-linux/debug/
```
After this you just start the debug server with the following command:
```sh
$ ./iotjs --start-debug-server --debugger-wait-source
```
You can also start your debug server and simultaneously add the file to debug:
```sh
$ ./iotjs --start-debug-server <path/to/filename.js>
```

### Debug using Launch

If you want to start your own debug server, choose **IoT.js: Launch** as your configuration instead. Then open your `launch.json` file and modify it to your needs:
If you want to launch the debug server locally, you need to modify the **program** attribute to the absolute path to the **iotjs** executable:
```json
{
"program": "/path/to/iotjs/build/x86_64-linux/debug/bin/iotjs"
}
```
If instead you want to debug on a remote device you will have to modify the **address** attribute from `localhost` to the IP Address of the remote device:
```json
{
"address": "<remote-device-ip>"
}
```
After successfully modifying the attribute you can start debugging by pressing `F5` or by clicking on the green triangle.

### Install Tizen Studio
If you want to create native applications for Tizen devices specifically you need [Tizen Studio](https://developer.tizen.org/ko/development/tizen-studio). You can download it from [HERE](https://developer.tizen.org/ko/development/tizen-studio/download#).
Now open a terminal then navigate to the directory where the installer was downloaded and apply the execute permission to the installer. For example:
```sh
$ sudo chmod +x web-cli_Tizen_Studio_2.5_ubuntu-64.bin
```
Then, execute the installer by entering the command with the following syntax:
```sh
web-cli_Tizen_Studio_<version> [options] [<directory path>]
```
For example:
```sh
$ ./web-cli_Tizen_Studio_2.5_ubuntu-64.bin --accept-license ~/tizen-studio
```

### Create Native Tizen Project

After successfully installing Tizen Studio CLI you can open VSCode and navigate to your project's `launch.json` file to modify it for later usage with Tizen Applications.
In `launch.json` you need to add two additional lines to the **launch** configuration:
```json
{
"tizenStudioPath": "/path/to/tizen-studio",
"IoTjsPath": "/path/to/iotjs"
}
```
These two lines will trigger a setup script which automatically installs all required packages related to creating a native Tizen IoTjs project. This will take several minutes but you can follow the progress on your debug console. After the script is done you will be able to create a IoTjs Tizen Project by clicking on its icon on the top right corner.

![tizen](/images/create-iotjs-tizen-project.png)

This will prompt an Input Box where you name your new Tizen Project. Pressing Enter will lead you to an Open Dialog window where you have to select the destination directory of your project.
After selecting your desired destination directory, your project will be automatically created and opened as a new workspace in VSCode.

### Package and Install Tizen Project to Tizen devices

When your Tizen Project is ready you are able to package and install it automatically to a remote Tizen device. This installation is done when you start to debug your code by pressing `F5` or clicking on the green triangle on the debug section. 

It only works if you have given all necessary information in your launch.json file:
```json
{
"address": "<tizen-device-ip>",
"tizenStudioPath": "/path/to/tizen-studio",
"IoTjsPath": "/path/to/iotjs",
"localRoot": "${workspaceRoot}"
}
```
You are able to follow the installation process through the debug console. Your installed application can be found on the remote device on this path:
`/opt/usr/globalapps/org.example.<projectname>`