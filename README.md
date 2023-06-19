[![CI](https://github.com/ktooi/ansible-role-crio/workflows/CI/badge.svg)](https://github.com/ktooi/ansible-role-crio/actions?query=workflow%3ACI+branch%3Amain)

日本語版は[こちら](README_ja.md)。

# Ansible Role: crio

Install CRI-O on a RHEL/CentOS or Debian/Ubuntu server.

This role will do the following.

* Load Kernel module.
* Configure Kernel parameters.
* Install CRI-O.
* Configure CRI-O.

## Requirements

There are no special requirements for this role.

## Role Variables

```yaml
crio_version: "1.27"
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

|Operating system|`$OS` (`crio_os`)|
|---|---|
|CentOS 7|CentOS_7|
|CentOS 8|CentOS_8|
|CentOS 8 Stream|CentOS_8_Stream|
|CentOS 9 Stream|CentOS_9_Stream|
|Debian 10|Debian_10|
|Debian 11|Debian_11|
|Fedora 36|Fedora_36|
|Fedora 37|Fedora_37|
|Fedora Rawhide|Fedora_Rawhide|
|Raspbian 10|Raspbian_10|
|Raspbian 11|Raspbian_11|
|Ubuntu 18.04|xUbuntu_18.04|
|Ubuntu 20.04|xUbuntu_20.04|
|Ubuntu 21.10|xUbuntu_21.10|
|Ubuntu 22.04|xUbuntu_22.04|

Note that the valid OS depends on the version of CRI-O. The available OS for each version can be found at [openSUSE Download](https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/).
Select the version of CRI-O you wish to install from [openSUSE Download](https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/) and check the directory name displayed.

If your OS has files under `vars/`, `crio_os` will be set automatically and you do not need to specify it.

```yaml
crio_version_url: '1.27'
```

This parameter is used as part of the URL of the CRI-O repository.
It may be in the form ``X.Y`` or ``X.Y:/X.Y.Z``. (e.g., `1.27` or `1.27:/1.27.0`)

You don't need to specify the value of `crio_version`, since it is automatically set from the value of `crio_version`.

```yaml
crio_long_version: '1.27'
```

This parameter specifies the version of CRI-O in long form.
It is specified in either `X.Y` or `X.Y:X.Y.Z` format. (e.g., `1.27` or `1.27:1.27.0`)
The format is similar to `crio_version_url`, but with or without `/`.

It is automatically set from the value of `crio_version`, so you don't need to specify it.

```yaml
crio_conf: {}
```

This parameter specifies the contents of crio.conf.
See https://github.com/cri-o/cri-o/blob/main/docs/crio.conf.5.md for parameters that can be specified in crio.conf.

`crio_conf` can be specified as follows:

```yaml
crio_conf:
  "table":
    option: value
  "table.subtable1":
    option: value
  "table.subtable2":
    option: value
```

In this case, crio.conf outputs the following:

```
[table]
option = value

[table.subtable1]
option = value

[table.subtable2]
option = value

```

See [crio_conf.yml.example](./defaults/crio_conf.yml.example) for a more realistic specificatioin.

## Role Tags

### crio-repo-key

The `crio-repo-key` tag can be used to update keys used in the CRI-O apt repository.

It does not perform tasks such as updating the apt repository cache, updating CRI-O packages, or changing settings. Use this function only for updating keys, such as when a key has expired.

## Dependencies

None.

## Example Playbook

```yaml
- hosts: crio_servers
  roles:
    - ktooi.crio
````

## Authors

* **Kodai Tooi** [GitHub](https://github.com/ktooi), [Qiita](https://qiita.com/ktooi)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details


Translated with www.DeepL.com/Translator (free version)
