# Developing apps in Eclipse

After you set up your development environment, you can import it into Eclipse to use the features in the Eclipse integrated development environment (IDE) to develop your app.

## Prerequsites

- [Python 3.x](https://www.python.org/downloads/)
- [PIP](https://pip.pypa.io/en/stable/installing/)
- [Eclipse IDE](https://eclipse.org/downloads/)

## Install PyDev Into Eclipse

Install PyDev into Eclipse for development.

1. To install from the Eclipse Marketplace, click **Help > Eclipse Marketplace** on the main Eclipse Help panel.
2. Paste the Pydev url (e.g., <http://pydev.org/updates>) into the 'work with:' field and press **Enter**.
3. Select the Pydev checkbox in the table below and click **Next**
4. Continue through wizard and accept the license agreement for Pydev.


## Create a New Project

Create a new project to develop your app in.

1. After you install PyDev, switch the perspective to **PyDev**.
2. In the PyDev perspective, click **File > New > PyDev project**.
3. In the PyDev Project window, type a project name.
4. In the **Grammar Version** list, select **3.6**.
5. In the Interpreter list, select **python3**.
6. Click **Create links to existing sources**, and click **Next**.
7. Click **Add external source folder**, browse to the root directory of your development workspace, click **OK**, and then click **Finish**.
The new Eclipse PyDev project that contains your development workspace appears in your Package Explorer.

## Install QPyLib for development

Download and install the latest version of qpylib into your Python site-packages library.

1. Check the latest tagged version of qpylib from this site: <https://github.com/IBM/qpylib/tags> (e.g., 2.0.1).
2. Download the latest version of qpylib from the public github repo by running the following commands:

```bash
QPYLIB_VERSION=2.0.1
curl -s -OL \
    https://github.com/IBM/qpylib/releases/download/${QPYLIB_VERSION}/qpylib-${QPYLIB_VERSION}.tar.gz
```

3. Install the QPyLib package using pip:

```bash
QPYLIB_VERSION=2.0.1
python -m pip install qpylib-${QPYLIB_VERSION}.tar.gz
```

4. If it is not already installed, install Flask into your local site-packages library by running the following command:

```bash
python -m pip install Flask
```

## Add the external library to the Pydev project

1. Right-click on the project in Eclipse and select **Properties**.
2. Select **PyDev - PYTHONPATH**.
3. Select **External Libraries**, then select **Add source folder**.
4. Browse to the site packages folder from your local site-packages folder for Python. If you are unsure
where this is, run the following command from a terminal window:

```bash
python -c 'import site; print(site.getsitepackages())'
```

5. Click **Apply**, then click **Close**.
