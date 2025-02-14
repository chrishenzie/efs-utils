[![CircleCI](https://circleci.com/gh/aws/efs-utils.svg?style=svg)](https://circleci.com/gh/aws/efs-utils)

# efs-utils

Utilities for Amazon Elastic File System (EFS)

The `efs-utils` package has been verified against the following Linux distributions:

| Distribution | Package Type | `init` System |
| ------------ | ------------ | ------------- |
| Amazon Linux 2017.09 | `rpm` | `upstart` |
| Amazon Linux 2 | `rpm` | `systemd` |
| CentOS 7 | `rpm` | `systemd` |
| CentOS 8 | `rpm` | `systemd` |
| RHEL 7 | `rpm`| `systemd` |
| RHEL 8 | `rpm`| `systemd` |
| Fedora 28 | `rpm` | `systemd` |
| Fedora 29 | `rpm` | `systemd` |
| Fedora 30 | `rpm` | `systemd` |
| Fedora 31 | `rpm` | `systemd` |
| Fedora 32 | `rpm` | `systemd` |
| Debian 9 | `deb` | `systemd` |
| Debian 10 | `deb` | `systemd` |
| Ubuntu 16.04 | `deb` | `systemd` |
| Ubuntu 18.04 | `deb` | `systemd` |
| Ubuntu 20.04 | `deb` | `systemd` |
| OpenSUSE Leap | `rpm` | `systemd` |
| OpenSUSE Tumbleweed | `rpm` | `systemd` |
| SLES 12 | `rpm` | `systemd` |
| SLES 15 | `rpm` | `systemd` |

The `efs-utils` package has been verified against the following MacOS distributions:

| Distribution | `init` System |
| ------------ | ------------- |
| MacOS Big Sur | `launchd` |

## Prerequisites

* `nfs-utils` (RHEL/CentOS/Amazon Linux/Fedora) or `nfs-common` (Debian/Ubuntu)
* OpenSSL 1.0.2+
* Python 3.4+
* `stunnel` 4.56+

## Installation

### On Amazon Linux distributions

For those using Amazon Linux or Amazon Linux 2, the easiest way to install `efs-utils` is from Amazon's repositories:

```
$ sudo yum -y install amazon-efs-utils
```

### Install via AWS Systems Manager Distributor
You can now use AWS Systems Manage Distributor to automatically install or update `amazon-efs-utils`. 
Please refer to [Using AWS Systems Manager to automatically install or update Amazon EFS clients](https://docs.aws.amazon.com/efs/latest/ug/manage-efs-utils-with-aws-sys-manager.html) for more guidance.

The following are prerequisites for using AWS Systems Manager Distributor to install or update `amazon-efs-utils`:

1. AWS Systems Manager agent is installed on the distribution (For `Amazon Linux` and `Ubuntu`, AWS Systems Manager agent
is pre-installed, for other distributions, please refer to [install AWS Systems Manager agent on Linux EC2 instance](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)
for more guidance.)

2. Instance is attached with IAM role with AWS managed policy `AmazonElasticFileSystemsUtils`, this policy will enable your instance to be managed by
 AWS Systems Manager agent, also it contains permissions to support specific features.

### On other Linux distributions

Other distributions require building the package from source and installing it.

- To build and install an RPM:

If the distribution is not OpenSUSE or SLES

```
$ sudo yum -y install git rpm-build make
$ git clone https://github.com/aws/efs-utils
$ cd efs-utils
$ make rpm
$ sudo yum -y install build/amazon-efs-utils*rpm
```

Otherwise

```
$ sudo zypper refresh
$ sudo zypper install -y git rpm-build make
$ git clone https://github.com/aws/efs-utils
$ cd efs-utils
$ make rpm
$ sudo zypper --no-gpg-checks install -y build/amazon-efs-utils*rpm
```

On OpenSUSE, if you see error like `File './suse/noarch/bash-completion-2.11-2.1.noarch.rpm' not found on medium 'http://download.opensuse.org/tumbleweed/repo/oss/'`
during installation of `git`, run the following commands to re-add repo OSS and NON-OSS, then run the install script above again.

```
sudo zypper ar -f -n OSS http://download.opensuse.org/tumbleweed/repo/oss/ OSS
sudo zypper ar -f -n NON-OSS http://download.opensuse.org/tumbleweed/repo/non-oss/ NON-OSS
sudo zypper refresh
```

- To build and install a Debian package:

```
$ sudo apt-get update
$ sudo apt-get -y install git binutils
$ git clone https://github.com/aws/efs-utils
$ cd efs-utils
$ ./build-deb.sh
$ sudo apt-get -y install ./build/amazon-efs-utils*deb
```

### On MacOS Big Sur distribution

For EC2 Mac instances running macOS Big Sur, you can install amazon-efs-utils from the [homebrew-aws](https://github.com/aws/homebrew-aws) respository.
```
brew install amazon-efs-utils
```

This will install amazon-efs-utils on your EC2 Mac Instance running macOS Big Sur in the directory `/usr/local/Cellar/amazon-efs-utils`. At the end of the installation, it will print a set of commands that must be executed in order to start using efs-utils. The instructions that are printed after amazon-efs-utils and must be executed are:

```
Perform below actions to start using efs:
    sudo mkdir -p /Library/Filesystems/efs.fs/Contents/Resources
    sudo ln -s /usr/local/bin/mount.efs /Library/Filesystems/efs.fs/Contents/Resources/mount_efs

Perform below actions to stop using efs:
    sudo rm /Library/Filesystems/efs.fs/Contents/Resources/mount_efs

To enable watchdog for using TLS mounts:
    sudo cp /usr/local/Cellar/amazon-efs-utils/<version>/libexec/amazon-efs-mount-watchdog.plist /Library/LaunchAgents
    sudo launchctl load /Library/LaunchAgents/amazon-efs-mount-watchdog.plist

To disable watchdog for using TLS mounts:
    sudo launchctl unload /Library/LaunchAgents/amazon-efs-mount-watchdog.plist
```

#### Run tests

- [Set up a virtualenv](http://libzx.so/main/learning/2016/03/13/best-practice-for-virtualenv-and-git-repos.html) for efs-utils

```
$ virtualenv ~/.envs/efs-utils
$ source ~/.envs/efs-utils/bin/activate
$ pip install -r requirements.txt
```

- Run tests

```
$ make test
```

## Usage

### mount.efs

`efs-utils` includes a mount helper utility to simplify mounting and using EFS file systems.

To mount with the recommended default options, simply run:

```
$ sudo mount -t efs file-system-id efs-mount-point/
```

To mount file system within a given network namespace, run:

```
$ sudo mount -t efs -o netns=netns-path file-system-id efs-mount-point/
```

To mount file system to the mount target in specific availability zone (e.g. us-east-1a), run:

```
$ sudo mount -t efs -o az=az-name file-system-id efs-mount-point/
```

To mount over TLS, simply add the `tls` option:

```
$ sudo mount -t efs -o tls file-system-id efs-mount-point/
```

To authenticate with EFS using the system’s IAM identity, add the `iam` option. This option requires the `tls` option.

```
$ sudo mount -t efs -o tls,iam file-system-id efs-mount-point/
```

To mount using an access point, use the `accesspoint=` option. This option requires the `tls` option.
The access point must be in the "available" state before it can be used to mount EFS.

```
$ sudo mount -t efs -o tls,accesspoint=access-point-id file-system-id efs-mount-point/
```

To mount your file system automatically with any of the options above, you can add entries to `/efs/fstab` like:

```
file-system-id efs-mount-point efs _netdev,tls,iam,accesspoint=access-point-id 0 0
```

For more information on mounting with the mount helper, see the manual page:

```
man mount.efs
```

or refer to the [documentation](https://docs.aws.amazon.com/efs/latest/ug/using-amazon-efs-utils.html).

### amazon-efs-mount-watchdog

`efs-utils` contains a watchdog process to monitor the health of TLS mounts. This process is managed by either `upstart` or `systemd` depending on your Linux distribution and `launchd` on Mac distribution, and is started automatically the first time an EFS file system is mounted over TLS.

## Upgrading stunnel for RHEL/CentOS

By default, when using the EFS mount helper with TLS, it enforces certificate hostname checking. The EFS mount helper uses the `stunnel` program for its TLS functionality. Please note that some versions of Linux do not include a version of `stunnel` that supports TLS features by default. When using such a Linux version, mounting an EFS file system using TLS will fail. 

Once you’ve installed the `amazon-efs-utils` package, to upgrade your system’s version of `stunnel`, see [Upgrading Stunnel](https://docs.aws.amazon.com/efs/latest/ug/using-amazon-efs-utils.html#upgrading-stunnel).

## Upgrading stunnel for SLES12

Run the following commands and follow the output hint of zypper package manager to upgrade the stunnel on your SLES12 instance

```bash
sudo zypper addrepo https://download.opensuse.org/repositories/security:Stunnel/SLE_12_SP5/security:Stunnel.repo
sudo zypper refresh
sudo zypper install -y stunnel
```

## Upgrading stunnel for MacOS

The installation installs latest stunnel available in brew repository. You can also upgrade the version of stunnel on your instance using the command below:
```
brew upgrade stunnel
```


## Enable mount success/failure notification via CloudWatch log
`efs-utils` now support publishing mount success/failure logs to CloudWatch log. By default, this feature is disabled. There are three
steps you must follow to enable and use this feature:

### Step 1. Install botocore
`efs-utils` uses botocore to interact with CloudWatch log service . Please note the package type from the above table. 
- Download the `get-pip.py` script
#### RPM
```bash
sudo yum -y install wget
```
```bash
if [[ "$(python3 -V 2>&1)" =~ ^(Python 3.5.*) ]]; then
    sudo wget https://bootstrap.pypa.io/3.5/get-pip.py -O /tmp/get-pip.py
elif [[ "$(python3 -V 2>&1)" =~ ^(Python 3.4.*) ]]; then
    sudo wget https://bootstrap.pypa.io/3.4/get-pip.py -O /tmp/get-pip.py
else
    sudo wget https://bootstrap.pypa.io/get-pip.py -O /tmp/get-pip.py
fi
```
#### DEB
```bash
sudo apt-get update
sudo apt-get -y install wget
```
```bash
if echo $(python3 -V 2>&1) | grep -e "Python 3.5"; then
    sudo wget https://bootstrap.pypa.io/3.5/get-pip.py -O /tmp/get-pip.py
elif echo $(python3 -V 2>&1) | grep -e "Python 3.4"; then
    sudo wget https://bootstrap.pypa.io/3.4/get-pip.py -O /tmp/get-pip.py
else
    sudo apt-get -y install python3-distutils
    sudo wget https://bootstrap.pypa.io/get-pip.py -O /tmp/get-pip.py
fi
```

- To install botocore on RPM
```bash
sudo python3 /tmp/get-pip.py
sudo pip3 install botocore || sudo /usr/local/bin/pip3 install botocore
```

- To install botocore on DEB
```bash
sudo python3 /tmp/get-pip.py
sudo pip3 install botocore || sudo /usr/local/bin/pip3 install botocore
```

#### On Debian10 and Ubuntu20, the botocore needs to be installed in specific target folder
```bash
sudo python3 /tmp/get-pip.py
sudo pip3 install --target /usr/lib/python3/dist-packages botocore || sudo /usr/local/bin/pip3 install --target /usr/lib/python3/dist-packages botocore
```

#### To install botocore on MacOS
```bash
sudo pip3 install botocore
```

### Step 2. Enable CloudWatch log feature in efs-utils config file `/etc/amazon/efs/efs-utils.conf`
```bash
sudo sed -i -e '/\[cloudwatch-log\]/{N;s/# enabled = true/enabled = true/}' /etc/amazon/efs/efs-utils.conf
```

- For MacOS:
```bash
sudo sed -i -e '/\[cloudwatch-log\]/{N;s/# enabled = true/enabled = true/;}' /usr/local/Cellar/amazon-efs-utils/<version>/etc/amazon/efs/efs-utils.conf
```
You can also configure CloudWatch log group name and log retention days in the config file. 

### Step 3. Attach the CloudWatch logs policy to the IAM role attached to instance.
Attach AWS managed policy `AmazonElasticFileSystemsUtils` to the iam role you attached to the instance, or the aws credentials
configured on your instance.

After completing the three prerequisite steps, you will be able to see mount status notifications in CloudWatch Logs.

## License Summary

This code is made available under the MIT license.

