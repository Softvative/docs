---
title: Linux Tentacle
description: Configuring Linux Tentacle deployment targets in Octopus.
position: 20
hideInThisSectionHeader: true
---

## Requirements

- Octopus Server 2019.8.3 or newer

Linux Tentacle is a .NET Core application distributed as a [self-contained deployment](https://docs.microsoft.com/en-us/dotnet/core/deploying/#self-contained-deployments-scd). On most Linux distributions it will "just work", but be aware that there are [.NET Core prerequisites](https://github.com/dotnet/core/blob/master/Documentation/prereqs.md) that may need to be installed.

## Known limitations

Support for ScriptCS and F# scripts are only available with **Mono 4** and above. While they require mono installed, they will still execute with the self-contained Calamari.

ScriptCS has not been ported for .NET Core ([GitHub issue](https://github.com/scriptcs/scriptcs/issues/1183)).

Similarly, the F# interpreter has also not yet been ported for .NET Core ([GitHub issue](https://github.com/Microsoft/visualfsharp/issues/2407)).

:::warning 
ScriptCS does not work on Mono **5.16** and higher. We recommend using Mono **5.14.x**.
:::

## Downloads

So far there is a .deb package for use with `apt-get` on Debian distributions, an .rpm package for use with `yum` on Fedora distributions, and a .tar.gz archive for manual installations. The packages are available from:

- apt.octopus.com
- rpm.octopus.com
- [Octopus Deploy downloads page](https://g.octopushq.com/ProductDownloadPage)

The latest release of Linux Tentacle is available for download from:

- [Archive](https://octopus.com/downloads/latest/Linux_x64TarGz/OctopusTentacle)
- [APT](https://octopus.com/downloads/latest/Linux_x64Apt/OctopusTentacle)
- [RPM](https://octopus.com/downloads/latest/Linux_x64Rpm/OctopusTentacle)

## Installing and configuring Linux Tentacle
Note that many of the steps described below must be run as a super user using `sudo`.

### Installing Tentacle
```bash Debian/Ubuntu repository
apt-key adv --fetch-keys https://apt.octopus.com/public.key
add-apt-repository "deb https://apt.octopus.com/ stretch main"
apt-get update
apt-get install tentacle
```

```bash CentOS/Fedora repository
wget https://rpm.octopus.com/tentacle.repo -O /etc/yum.repos.d/tentacle.repo
yum install tentacle
```

```bash Archive
wget https://octopus.com/downloads/latest/Linux_x64TarGz/OctopusTentacle -O tentacle-linux_x64.tar.gz
#or
curl -L https://octopus.com/downloads/latest/Linux_x64TarGz/OctopusTentacle --output tentacle-linux_x64.tar.gz

mkdir /opt/octopus
tar xvzf tentacle-linux_x64.tar.gz -C /opt/octopus
```

### Setting up a Tentacle instance
Many instances of Tentacle can be configured on a single machine. To configure an instance run the following setup script:

```bash
/opt/octopus/tentacle/configure-tentacle.sh
```

Additional instances of Tentacle can be created and configured by passing the `--instance $instanceName` argument to all of the commands listed here.

## Running Tentacle

### Running Tentacle interactively
Start the Tentacle interactively by running:

```
/opt/octopus/tentacle/Tentacle run --instance <instance name>
```

### Running Tentacle as a service (systemd)
Tentacle has command line options for configuring a systemd service:

```
Usage: Tentacle service [<options>]

Where [<options>] is any of:

      --instance=VALUE       Name of the instance to use
      --start                Start the Windows Service if it is not already
                               running
      --stop                 Stop the Windows Service if it is running
      --reconfigure          Reconfigure the Windows Service
      --install              Install the Windows Service
      --username, --user=VALUE
                             Username to run the service under
                               (DOMAIN\Username format). Only used when --
                               install or --reconfigure are used.
      --uninstall            Uninstall the Windows Service
      --password=VALUE       Password for the username specified with --
                               username. Only used when --install or --
                               reconfigure are used.
      --dependOn=VALUE

Or one of the common options:

      --help                 Show detailed help for this command
```

To install and start Tentacle as a service, use the `Tentacle service` command:

```
 /opt/octopus/tentacle/Tentacle service --install --start
```

### Manually configuring Tentacle to run as a service
To manually configure a systemd service, use the following sample unit file:

1. Create a systemd **Unit file** to run Tentacle.
    ```
    [Unit]
    Description=Octopus Tentacle Server
    After=network.target

    [Service]
    Type=simple
    User=root
    ExecStart=/opt/octopus/tentacle/Tentacle run --instance <instance name> --noninteractive
    Restart=always

    [Install]
    WantedBy=multi-user.target
    ```

2. Copy the unit file to `/etc/systemd/system` and give it permissions
    ```
    sudo cp tentacle.service /etc/systemd/system/tentacle.service
    sudo chmod 644 /etc/systemd/system/tentacle.service
    ```

3. Start the Tentacle service
    ```
    sudo systemctl start tentacle
    ```

4. Use the `enable` command to ensure that the service start whenever the system boots.
    ```
    sudo systemctl enable tentacle
    ```

## Automatic Tentacle upgrades
Linux Tentacle can be upgraded via the Octopus portal from the {{Infrastructure > Deployment Targets}} screen. The upgrade attempts to find a package manager capable of performing the upgrade, and then falls back to extracting a `tar.gz` archive to the Tentacle installation folder.

The upgrade is attempted in the following order:

- Attempt to use `apt-get`
- Attempt to use `yum`
- Extract the bundled `tar.gz` archive

## Uninstall Tentacle

To uninstall (delete) a Tentacle instance run the `service --stop --uninstall` and then `delete-instance` commands first:

```
/opt/octopus/tentacle/Tentacle service --instance <instance name> --stop --uninstall
/opt/octopus/tentacle/Tentacle delete-instance <instance name>
```

The `service --stop --uninstall` command on the Tentacle will run the following commands to manage the systemd **Unit file**:

```
sudo systemctl stop tentacle
sudo systemctl disable tentacle
sudo rm /etc/systemd/system/tentacle.service
```

Then the working folders and logs can be deleted if they are no longer needed, depending on where you installed them, for instance:
```
# default locations:
# - installed directory:
cd /opt/octopus/tentacle

# - logs:
cd /etc/octopus

# - application directory:
cd /home/Octopus/Applications
```

## Automation scripts
The following bash scripts install, configure and register Linux Tentacle for use in automated environments:

!include <quickstart-debian>

!include <quickstart-fedora>

!include <quickstart-archive>

## Learn more

- [Linux blog posts](https://octopus.com/blog/tag/linux)
