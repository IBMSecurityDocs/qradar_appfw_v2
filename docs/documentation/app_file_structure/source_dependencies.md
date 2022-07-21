# Source dependencies

If your app requires dependencies, such as RPMs or Python libraries, you can add them in the container subdirectory of
the app folder.

The container directory can contain these optional subdirectories:

## pip

Use the pip folder to install extra Python libraries. For example, if your application requires the `observable-0.01.00`
Python library, add the `observable-0.01.00.tar.gz` file to the pip folder.

Don't use `.tar` files for Python libraries that include extra C-based extensions. Instead, add libraries as Python
wheel files (`.whl`), which have C-based extensions pre-compiled.

You must install Python wheel files on the same system architecture they were compiled upon. To work with IBM® QRadar®
application framework v2, wheel files must be compiled on RHEL 8 x86_64. If it uses compatible architecture, you can
use the Python bdist_wheel command to create wheel files from a library's source code on your own system. The command
python `setup.py sdist bdist_wheel` creates the wheel file when you run it from within the root directory of the Python
library's source folder.

A useful alternative to manually downloading Python packages for your app is the `pip2pi` Python package. It requires
pip and you can install it on your development computer by using the `pip install pip2pi` command. After you install
this package, you run the following command:

```bash
pip2tgz <target-directory> <Python package>
```

For example, the following command downloads the package's wheel, along with its dependencies, into the specified
folder.

```bash
pip2tgz python_packages/pytest/ pytest==2.8.2
```

The `==` parameter is optional after pytest in the command above and is used to download a specific version of the
package.

For Python libraries that have dependencies, you can include an optional `ordering.txt` file in the pip folder to specify
the order to install Python libraries. This text file must include the names of files that are in the pip
folder. File names must be separated with a new line (UNIX line endings) in the order that you want them installed.

```txt
pytz-2020.1-py2.py3-none-any.whl
Babel-2.8.0-py2.py3-none-any.whl
speaklater-1.3.tar.gz
Flask-Babel-1.0.0.tar.gz
```

## rpm

Use the rpm folder to install extra Red Hat Enterprise Linux (RHEL) RPMs. The RPMs must be RHEL 8 x86_64 compatible.

For RPMs that have dependencies, you can include an optional `ordering.txt` file in the rpms folder to specify the order
to install RPMs. This text file must include the names of files that are in the rpms folder. File names
must be separated with a new line (UNIX line endings) in the order that you want them installed.

```txt
nginx-1.17.8-1.el8.ngx.x86_64.rpm
nodejs-10.19.0-1.module+el8.1.0+5726+6ed65f8c.x86_64.rpm
npm-6.13.4-1.10.19.0.1.module+el8.1.0+5726+6ed65f8c.x86_64.rpm
```
