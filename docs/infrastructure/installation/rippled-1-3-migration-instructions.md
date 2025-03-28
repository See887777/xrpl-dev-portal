---
html: rippled-1-3-migration-instructions.html
parent: install-rippled.html
seo:
    description: Use these instructions to upgrade rippled packages from 1.2.x or below to 1.3.x or higher.
status: removed
---
# rippled v1.3.x Migration Instructions

This document describes the migration process for upgrading from `rippled` 1.2.4 or earlier to `rippled` v1.3 or later. This migration process is necessary because the `rippled` install process has changed as of version 1.3.

This document provides migration steps for upgrading on supported platforms:

- [CentOS or Red Hat Enterprise Linux (RHEL)](#migration-on-centos-or-red-hat-enterprise-linux-rhel)
- [Ubuntu Linux](#migration-on-ubuntu-linux)

For other platforms, see the updated instructions for compiling from source. ([Ubuntu](build-on-linux-mac-windows.md), [macOS](build-on-linux-mac-windows.md), or [Windows](https://github.com/XRPLF/rippled/tree/develop/Builds/VisualStudio2017))


## Migration on CentOS or Red Hat Enterprise Linux (RHEL)

Ripple's official RPM repository and instructions for using it have changed. If you have [automatic updates](update-rippled-automatically-on-linux.md) enabled, your system should perform the migration automatically. To migrate manually from the old repository to the new one, complete the following steps:

1. Stop the `rippled` server.

    ```
    $ sudo systemctl stop rippled.service
    ```

2. Remove the old Ripple repository package.

    ```
    $ sudo rpm -e ripple-repo
    ```

    The `rippled-repo` package is now **DEPRECATED**. The package has been updated one last time for version 1.3.1. In the future, any changes to the repositories will require manual changes to the `ripple.repo` file. <!-- STYLE_OVERRIDE: will -->

3. Add Ripple's new yum repository:

    ```
    $ cat << REPOFILE | sudo tee /etc/yum.repos.d/ripple.repo
    [ripple-stable]
    name=XRP Ledger Packages
    baseurl=https://repos.ripple.com/repos/rippled-rpm/stable/
    enabled=1
    gpgcheck=0
    gpgkey=https://repos.ripple.com/repos/rippled-rpm/stable/repodata/repomd.xml.key
    repo_gpgcheck=1
    REPOFILE
    ```

4. Install the new `rippled` package:

    ```
    $ sudo yum install rippled
    ```

    Version 1.3.1 does not require any changes to your config files (`rippled.cfg` and `validators.txt`). This update procedure leaves your existing config files in place.

5. Reload systemd unit files:

    ```
    $ sudo systemctl daemon-reload
    ```

6. Start the `rippled` service:

    ```
    $ sudo systemctl start rippled.service
    ```


{% admonition type="danger" name="Warning" %}If you use [automatic updates](update-rippled-automatically-on-linux.md), they should continue working after performing this migration process. However, **the `ripple-repo` package is now deprecated**. As a consequence, in the future, any changes to Ripple's repositories may require you to manually update your repos file.{% /admonition %}


## Migration on Ubuntu Linux

Prior to version 1.3, the supported way to install `rippled` on Ubuntu Linux was using Alien to install the RPM package. Starting with `rippled` v1.3.1, Ripple provides a native package for Ubuntu and Debian Linux, which is the recommended way of installing it. If you already have the RPM package installed, complete the [installation steps](install-rippled-on-ubuntu.md) to upgrade the package and switch over to the native APT (`.deb`) package.

If you have made any changes to your config files (`/opt/ripple/etc/rippled.cfg` and `/opt/ripple/etc/validators.txt`), `apt` may prompt you during installation asking if you want to overwrite your config files with the newest versions from the packages. Version 1.3 does not require any changes to the config file, so you can safely keep your existing config files unchanged.

After installing the native APT package for 1.3, you need to reload/restart the service:

1. Reload systemd unit files:

    ```
    $ sudo systemctl daemon-reload
    ```

2. Restart the `rippled` service:

    ```
    $ sudo systemctl restart rippled.service
    ```

If you no longer need Alien for any other packages, you may optionally uninstall it and its dependencies using the following steps:

1. Uninstall Alien:

    ```
    $ sudo apt -y remove alien
    ```

2. Uninstall unused dependencies:

    ```
    $ sudo apt -y autoremove
    ```

### Automatic Updates

The `rippled` v1.3 package includes an updated auto-update script that works on Ubuntu and Debian Linux. For more information, see [Update `rippled` Automatically on Linux](update-rippled-automatically-on-linux.md).

## See Also

- **[`rippled` v1.3.1 Release Notes](https://github.com/XRPLF/rippled/releases/1.3.1)**
- **Concepts:**
    - [The `rippled` Server](../../concepts/networks-and-servers/index.md)
    - [Consensus](../../concepts/consensus-protocol/index.md)
- **Tutorials:**
    - [Update Automatically on Linux](update-rippled-automatically-on-linux.md)
    - [Troubleshoot rippled](../troubleshooting/index.md)
    - [Get Started with the rippled API](../../tutorials/http-websocket-apis/build-apps/get-started.md)
- **References:**
    - [rippled API Reference](../../references/http-websocket-apis/index.md)
        - [`rippled` Commandline Usage](../commandline-usage.md)
        - [server_info method][]

{% raw-partial file="/docs/_snippets/common-links.md" /%}
