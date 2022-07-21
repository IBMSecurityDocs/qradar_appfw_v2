# RPM/Python module best practices

This tutorial covers best practices for sourcing and downloading RPMs and Python Modules, including the following items:

-   Where to source official UBI RPMs and best practices on how to download them
-   Where to source python modules and how to download them
-   Handling dependencies

## Prerequisites

- pip
- [Python](https://www.python.org/downloads/) 3.6.8 or later
- QRadar app SDK version 2

## RPM

RPM Package Manager is a package management system intended for primarily Linux distributions and is widely used to deploy commerical and open source software. 
The name RPM refers to the `.rpm` file format and the package manager program itself.

### Where to source official UBI RPMs and how to download them 

Create a new directory called `container/rpm` to store RPMs that will be downloaded:

```bash
mkdir container/rpm
```

You can download RPMs using Docker and the Red Hat UBI. For example, use the following code to download the nodejs-10.21.0 and npm-6-14.4 RPMs:

```bash
docker run                                                    \
    -v $(pwd)/container/rpm:/rpm                              \
    registry.access.redhat.com/ubi8/ubi                       \
    yum download nodejs-10.21.0 npm-6.14.4 --downloaddir=/rpm
```

This uses the RHEL UBI 8 OS running inside Docker to download the RPMs, which can be used by QRadar to install them into an app.
The `-v $(pwd)/container/rpm:/rpm` flag creates a volume that means any RPMs downloded in the Docker container are copied over to the host machine into the `./container/rpm` path.

Create a new folder called `container/rpm/ordering.txt`, which instructs QRadar where to find the RPMs to install. In this example, if you install the nodejs-10.21.0 and npm-6-14.4 RPMs, you must save the following RPMs into the `ordering.txt` file:

```bash
nodejs-10.21.0-3.module+el8.2.0+7071+d2377ea3.x86_64.rpm
npm-6.14.4-1.10.21.0.3.module+el8.2.0+7071+d2377ea3.x86_64.rpm
```

## Python modules

Modules are simply Python files that have a `.py` extension. A Python module can have a set of functions, classes, or variables defined and implemented. 

### Where to source Python modules and how to download them

Create a new folder  called `container/pip` to store pip dependencies:

```bash
mkdir container/pip
```

You can use `pip download` to download Python modules. In this example, use the following command to download the Gunicorn pip and any of its dependencies:

```bash
pip download                \
    --only-binary=:all:     \
    --platform linux_x86_64 \
    --dest container/pip    \
    gunicorn
```

## Handling dependencies 

For RPMs that have dependencies, you can include an optional `ordering.txt` file in the RPM folder, like the one you created in the previous RPM example. 
This file specifies the order in which RPMs are installed, and it must include the names of the files that are in the rpm folder. 
Separate file names with a new line (UNIX line endings) in the order they are to be installed. 
