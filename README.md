[![CI](https://github.com/ktooi/ansible-role-crio/workflows/CI/badge.svg)](https://github.com/ktooi/ansible-role-crio/actions?query=workflow%3ACI+branch%3Amain)

# Ansible Role: crio

Install CRI-O on a RHEL/CentOS or Debian/Ubuntu server.

This role will do the following.

* Load Kernel module.
* Configure Kernel parameters.
* Install CRI-O.

This role does not do the following

* Configure CRI-O.

## Requirements

There are no special requirements for this role.

## Role Variables

```yaml
crio_version: "1.23"
````

Specifies the version of CRI-O to install.
You can specify either "X.Y" or "X.Y.Z" format.
Cannot include `*`.

```yaml
crio_sysctl_filename: "/etc/sysctl.d/99-kubernetes-cri.conf"
```

Specifies the filename to save the Kernel parameter settings.

```yaml
crio_os: Debian_10
````

Specifies the OS of the host on which to install.

The value to specify for each OS is the value of `$OS` as described in [cri-o installation instructions](https://cri-o.io/), but as of May 2022, the linked information seems to be out of date.
From my research, the currently valid OS specification is as follows.

|Operating system|``$OS` (`crio_os`)|
|---|---|
|CentOS 7|CentOS_7|
|CentOS 8|CentOS_8|
|CentOS 8 Stream|CentOS_8_Stream|
|Debian 10|Debian_10|
|CentOS 8 Stream|CentOS_8_Stream|CentOS 10|Debian_10|Debian 11|Debian_11|
|CentOS 8 Stream|CentOS_8_Stream|Debian 10|Debian_10|Debian 11|Debian_11|
|Fedora Rawhide|Fedora_Rawhide|
|Raspbian 10|Raspbian_10|
|Raspbian 10|Raspbian_10||Raspbian 11|Raspbian_11|
|Ubuntu 18.04|xUbuntu_18.04|
|Ubuntu 20.04|xUbuntu_20.04|
|Ubuntu 21.10|xUbuntu_21.10|
|Ubuntu 22.04|xUbuntu_22.04|

Note that the valid OS depends on the version of CRI-O. The available OS for each version can be found at [/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/ - openSUSE Download](https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/).
Select the version of CRI-O you wish to install from [openSUSE Download]() and check the directory name displayed.

If your OS has files under `vars/`, `crio_os` will be set automatically and you do not need to specify it.

```yaml
crio_version_url: '1.24'
```

This parameter is used as part of the URL of the CRI-O repository.
It may be in the form ``X.Y`` or ``X.Y:/X.Y.Z``. (e.g., `1.24` or `1.24:/1.24.0`)

You don't need to specify the value of `crio_version`, since it is automatically set from the value of `crio_version`.

```yaml
crio_long_version: '1.24'.
```

This parameter specifies the version of CRI-O in long form.
It is specified in either `X.Y` or `X.Y:X.Y.Z` format. (e.g., `1.24` or `1.24:1.24.0`)
The format is similar to `crio_version_url`, but with or without `/`.

It is automatically set from the value of `crio_version`, so you don't need to specify it.

## Dependencies

None.

## Example Playbook

```yaml
- hosts: crio-servers
  roles:
    - ktooi.crio
````

## Authors

* **Kodai Tooi** [GitHub](https://github.com/ktooi), [Qiita](https://qiita.com/ktooi)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details


Translated with www.DeepL.com/Translator (free version)
