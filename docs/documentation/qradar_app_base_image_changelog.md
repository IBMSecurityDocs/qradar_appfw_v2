# QRadar app base image changelog

This changelog tracks `qradar-app-base` image versions, documenting changes between versions.

There are three base image streams with major versions 2, 3, and 4. To find out more about the differences between
these streams, see the [QRadar app base image streams](./qradar_app_base_streams.md) page.

Each release pulls in any security fixes using patch versions provided by Red Hat.

## QRadar app base image mapping to QRadar releases

This table maps QRadar app base image versions to the QRadar release and QRadar App SDK release they were introduced in.

<table>
    <thead>
        <tr>
            <th>QRadar app base image version</th>
            <th>Introduced in QRadar version(s)</th>
            <th>Introduced in QRadar App SDK version</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>4.0.2</td>
            <td>7.5.0 UP7</td>
            <td>2.2.0</td>
        </tr>
        <tr>
            <td>3.0.11</td>
            <td>7.5.0 UP7</td>
            <td>2.2.0</td>
        </tr>
        <tr>
            <td>3.0.10</td>
            <td>7.5.0 UP6</td>
            <td>2.1.4</td>
        </tr>
        <tr>
            <td>3.0.9</td>
            <td>7.5.0 UP5, 7.4.3 FP9</td>
            <td>2.1.3</td>
        </tr>
        <tr>
            <td>3.0.5</td>
            <td>7.5.0 UP4, 7.4.3 FP8</td>
            <td>2.1.2</td>
        </tr>
        <tr>
            <td>3.0.1</td>
            <td>7.5.0 UP3</td>
            <td></td>
        </tr>
        <tr>
            <td>3.0.0</td>
            <td></td>
            <td>2.1.1</td>
        </tr>
        <tr>
            <td>2.1.18</td>
            <td>7.5.0 UP7</td>
            <td>2.2.0</td>
        </tr>
        <tr>
            <td>2.1.17</td>
            <td>7.5.0 UP6</td>
            <td>2.1.4</td>
        </tr>
        <tr>
            <td>2.1.16</td>
            <td>7.5.0 UP5, 7.4.3 FP9</td>
            <td>2.1.3</td>
        </tr>
        <tr>
            <td>2.1.12</td>
            <td>7.5.0 UP4, 7.4.3 FP8</td>
            <td>2.1.2</td>
        </tr>
        <tr>
            <td>2.1.8</td>
            <td>7.5.0 UP3, 7.4.3 FP7</td>
            <td>2.1.1</td>
        </tr>
        <tr>
            <td>2.1.7</td>
            <td>7.5.0 UP2, 7.4.3 FP6, 7.3.3 FP12</td>
            <td></td>
        </tr>
        <tr>
            <td>2.1.6</td>
            <td>7.5.0 UP1, 7.4.3 FP5, 7.3.3 FP11</td>
            <td>2.1.0</td>
        </tr>
        <tr>
            <td>2.1.3</td>
            <td>7.5.0 GA, 7.4.3 FP3, 7.3.3 FP10</td>
            <td></td>
        </tr>
        <tr>
            <td>2.0.5</td>
            <td>7.4.3 CR, 7.4.2 FP3, 7.3.3 FP8</td>
            <td></td>
        </tr>
        <tr>
            <td>2.0.4</td>
            <td>7.4.2 GA, 7.4.1 FP1, 7.3.3 FP6</td>
            <td>2.0.0</td>
        </tr>
    </tbody>
</table>

## 4.0.2

- New qradar-app-base image release stream, uses Python 3.11.

## 3.0.11

- Upgraded packages to resolve security vulnerabilities.
- Removed redundant Python packages.
- Updated qpylib version to [2.0.8](https://github.com/IBM/qpylib/releases/tag/2.0.8).

## 3.0.10

- Upgraded packages to resolve security vulnerabilities.
- Updated qpylib version to [2.0.7](https://github.com/IBM/qpylib/releases/tag/2.0.7).

## 3.0.9

- Upgraded packages to resolve security vulnerabilities.

## 3.0.8

- Upgraded packages to resolve security vulnerabilities.

## 3.0.7

- Upgraded packages to resolve security vulnerabilities.

## 3.0.6

- Upgraded packages to resolve security vulnerabilities.

## 3.0.5

- Upgraded packages to resolve security vulnerabilities.

## 3.0.1

- Upgraded packages to resolve security vulnerabilities.

## 3.0.0

- New qradar-app-base image release stream, uses Python 3.8.

## 2.1.18

- Upgraded packages to resolve security vulnerabilities.
- Removed redundant Python packages.
- Updated qpylib version to [2.0.8](https://github.com/IBM/qpylib/releases/tag/2.0.8).

## 2.1.17

- Upgraded packages to resolve security vulnerabilities.
- Updated qpylib version to [2.0.7](https://github.com/IBM/qpylib/releases/tag/2.0.7).

## 2.1.16

- Upgraded packages to resolve security vulnerabilities.

## 2.1.15

- Upgraded packages to resolve security vulnerabilities.

## 2.1.14

- Upgraded packages to resolve security vulnerabilities.

## 2.1.13

- Upgraded packages to resolve security vulnerabilities.

## 2.1.12

- Upgraded packages to resolve security vulnerabilities.

## 2.1.8

- Upgraded packages to resolve security vulnerabilities.

## 2.1.7

- Upgraded packages to resolve security vulnerabilities.

## 2.1.6

- Upgraded packages to resolve security vulnerabilities.

## 2.1.5

- Upgraded packages to resolve security vulnerabilities.

## 2.1.4

- Upgraded `click` from `7.0.0` to `8.0.0`.
- Upgraded packages to resolve security vulnerabilities.

## 2.1.3

- Updated qpylib version to [2.0.6](https://github.com/IBM/qpylib/releases/tag/2.0.6).

## 2.1.2

- Updated qpylib version to [2.0.5](https://github.com/IBM/qpylib/releases/tag/2.0.5).

## 2.1.1

- Upgraded packages to resolve security vulnerabilities.

## 2.1.0

- Upgraded packages to resolve security vulnerabilities.
- Switched a number of packages from being provided by PyPi to being provided by Red Hat repositories.
    - `python3-cffi` - changed from version `1.13.2` to `1.11.5`.
    - `python3-cryptography` - changed from version `2.8` to `3.2.1`.
    - `python3-urllib3` - changed from version `1.25.8` to `1.24.2`.
    - `python3-requests` - changed from version `2.22.0` to `2.25.1`.
    - `python3-pysocks` - changed from version `1.7.1` to `1.6.8`.
    - `python3-six` - changed from version `1.14.0` to `1.11.0`.
    - `python3-jinja2` - changed from version `2.11.1` to `2.10.1`.
    - `python3-chardet` - version maintained at `3.0.4`.
    - `python3-setuptools` - version maintained at `39.2.0`.
- Updated qpylib version to [2.0.4](https://github.com/IBM/qpylib/releases/tag/2.0.4).

## 2.0.6

- Upgraded packages to resolve security vulnerabilities.

## 2.0.5

- Upgraded packages to resolve security vulnerabilities.

## 2.0.4

- Upgraded packages to resolve security vulnerabilities.
- Updated qpylib version to [2.0.3](https://github.com/IBM/qpylib/releases/tag/2.0.3).

## 2.0.3

- Upgraded packages to resolve security vulnerabilities.
- Updated qpylib version to [2.0.2](https://github.com/IBM/qpylib/releases/tag/2.0.2).

## 2.0.2

- Upgraded packages to resolve security vulnerabilities.
- Added RPMs to support FIPS.

## 2.0.1

- Upgraded packages to resolve security vulnerabilities.
- Updated qpylib version to [2.0.1](https://github.com/IBM/qpylib/releases/tag/2.0.1).

## 2.0.0

- First release of the QRadar app base image v2.
