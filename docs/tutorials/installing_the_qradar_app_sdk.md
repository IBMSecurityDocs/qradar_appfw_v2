# Installing the QRadar App SDK

You can install the IBM® QRadar® App SDK on Windows, Linux, or MacOS operating systems.

## Prerequsites

- [Python](https://www.python.org/downloads/) 3.7 or later

**Note:** To install the SDK on Windows, you must have [WSL (Windows Subsystem for Linux)
installed](https://docs.microsoft.com/en-us/windows/wsl/install-win10).

## Procedure

1. Download and extract the QRadar App archive (.zip file) from
<https://exchange.xforce.ibmcloud.com/hub/extension/517ff786d70b6dfa39dde485af6cbc8b>.
2. Extract the SDK to a directory of your choice.
3. Open the command line and navigate to the directory you chose to extract the SDK installation archive.
4. Type the following command and press **Enter**:

```bash
./install.sh
```

> **Tip:** Do not use sudo to run the install script. Admin privileges are not required.

The SDK is installed under your home directory. The default location is a sub-directory named `qradarappsdk`. The
installation script asks if you want to choose a different location. If the directory you select is not empty, the installation
script warns you that the directory will be wiped and ask for confirmation before proceeding.
