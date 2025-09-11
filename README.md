[![CI](https://github.com/ktooi/ansible-role-crio/workflows/CI/badge.svg)](https://github.com/ktooi/ansible-role-crio/actions?query=workflow%3ACI+branch%3Amain)
[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-ktooi.crio-blue.svg)](https://galaxy.ansible.com/ktooi/crio)

日本語版は[こちら](README_ja.md)。

# Ansible Role: CRI-O

An Ansible role to install and configure CRI-O (Container Runtime Interface for OCI) on RHEL/Rocky Linux, Debian/Ubuntu, and Fedora systems.

## Overview

This role automates the installation and configuration of CRI-O, providing a lightweight, secure container runtime for Kubernetes. It handles all necessary system preparations including kernel modules, system parameters, repository setup, and service configuration.

### Features

- **Multi-OS Support**: RHEL/Rocky Linux, Debian/Ubuntu, Fedora, and Raspbian
- **Flexible Version Management**: Support for multiple CRI-O versions
- **Kernel Configuration**: Automatic kernel module loading and parameter tuning
- **Repository Management**: Automated repository setup and key management
- **Configuration Management**: Template-based CRI-O configuration with customizable options

## Requirements

- Ansible 2.9 or higher
- Target systems must have internet access for package installation
- Supported operating systems (see [Supported Operating Systems](#supported-operating-systems))

## Role Variables

### Core Configuration

```yaml
crio_version: "1.28"
```

Specifies the version of CRI-O to install. You can specify either "X.Y" or "X.Y.Z" format.
Cannot include `*`. Default is `1.28` (latest stable version available in repositories).

```yaml
crio_sysctl_filename: "/etc/sysctl.d/99-kubernetes-cri.conf"
```

Specifies the filename to save the kernel parameter settings.

### Operating System Configuration

```yaml
crio_os: Debian_12
```

Specifies the OS identifier for repository configuration. This is automatically detected for supported systems, but can be overridden if needed.

## Supported Operating Systems

The following operating systems are supported with CRI-O 1.28:

| Operating System | `crio_os` Value | Status |
|---|---|---|
| Rocky Linux 8 | `CentOS_8` | ✅ Supported |
| Rocky Linux 9 | `CentOS_9` | ✅ Supported |
| CentOS 9 Stream | `CentOS_9_Stream` | ✅ Supported |
| Red Hat Enterprise Linux 8 | `CentOS_8` | ✅ Supported |
| Red Hat Enterprise Linux 9 | `CentOS_9` | ✅ Supported |
| Debian 10 | `Debian_10` | ✅ Supported |
| Debian 11 | `Debian_11` | ✅ Supported |
| Debian 12 | `Debian_12` | ✅ Supported |
| Fedora 36 | `Fedora_36` | ✅ Supported |
| Fedora 37 | `Fedora_37` | ✅ Supported |
| Fedora 38 | `Fedora_38` | ✅ Supported |
| Fedora 39 | `Fedora_39` | ✅ Supported |
| Fedora Rawhide | `Fedora_Rawhide` | ✅ Supported |
| Raspbian 10 | `Raspbian_10` | ✅ Supported |
| Raspbian 11 | `Raspbian_11` | ✅ Supported |
| Ubuntu 20.04 | `xUbuntu_20.04` | ✅ Supported |
| Ubuntu 22.04 | `xUbuntu_22.04` | ✅ Supported |

### Legacy Operating Systems (No longer supported)

The following operating systems are no longer supported due to end-of-life status:

| Operating System | `crio_os` Value | Status |
|---|---|---|
| CentOS 7 | `CentOS_7` | ❌ EOL (June 2024) |
| CentOS 8 | `CentOS_8` | ❌ EOL (December 2021) |
| Ubuntu 18.04 | `xUbuntu_18.04` | ❌ EOL (April 2023) |
| Ubuntu 21.10 | `xUbuntu_21.10` | ❌ EOL (July 2022) |

> **Note**: Available OS support depends on the CRI-O version. Check the [openSUSE Build Service](https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/) for version-specific OS availability.

If your OS has configuration files under `vars/`, `crio_os` will be set automatically and you do not need to specify it manually.

### Advanced Configuration

```yaml
crio_version_url: '1.28'
```

This parameter is used as part of the URL of the CRI-O repository.
It may be in the form `X.Y` or `X.Y:/X.Y.Z`. (e.g., `1.28` or `1.28:/1.28.0`)

This is automatically set from the value of `crio_version`, so you typically don't need to specify it.

```yaml
crio_long_version: '1.28'
```

This parameter specifies the version of CRI-O in long form.
It is specified in either `X.Y` or `X.Y:X.Y.Z` format. (e.g., `1.28` or `1.28:1.28.0`)

This is automatically set from the value of `crio_version`, so you typically don't need to specify it.

### CRI-O Configuration

```yaml
crio_conf: {}
```

This parameter specifies the contents of `/etc/crio/crio.conf`.
See the [CRI-O configuration documentation](https://github.com/cri-o/cri-o/blob/main/docs/crio.conf.5.md) for available parameters.

Example configuration:

```yaml
crio_conf:
  "crio":
    log_level: "info"
    log_dir: "/var/log/crio/pods"
  "crio.runtime":
    default_runtime: "runc"
    no_pivot: false
  "crio.image":
    default_transport: "docker://"
    pause_image: "registry.k8s.io/pause:3.9"
  "crio.network":
    network_dir: "/etc/cni/net.d/"
    plugin_dirs: ["/opt/cni/bin/"]
```

This generates the following configuration:

```ini
[crio]
log_level = "info"
log_dir = "/var/log/crio/pods"

[crio.runtime]
default_runtime = "runc"
no_pivot = false

[crio.image]
default_transport = "docker://"
pause_image = "registry.k8s.io/pause:3.9"

[crio.network]
network_dir = "/etc/cni/net.d/"
plugin_dirs = ["/opt/cni/bin/"]
```

See [crio_conf.yml.example](./defaults/crio_conf.yml.example) for a more comprehensive configuration example.

## Role Tags

### `crio-repo-key`

The `crio-repo-key` tag can be used to update repository keys used in the CRI-O apt repository.

```bash
ansible-playbook -i inventory playbook.yml --tags crio-repo-key
```

This tag only performs key updates and does not:
- Update the apt repository cache
- Update CRI-O packages
- Change configuration settings

Use this when repository keys have expired or need to be refreshed.

## Dependencies

None.

## Example Playbooks

### Basic Installation

```yaml
---
- hosts: crio_servers
  become: yes
  roles:
    - ktooi.crio
```

### Custom Version and Configuration

```yaml
---
- hosts: crio_servers
  become: yes
  vars:
    crio_version: "1.28"
    crio_conf:
      "crio":
        log_level: "info"
      "crio.runtime":
        default_runtime: "runc"
      "crio.image":
        pause_image: "registry.k8s.io/pause:3.9"
  roles:
    - ktooi.crio
```

### Kubernetes Cluster Setup

```yaml
---
- hosts: k8s_nodes
  become: yes
  vars:
    crio_version: "1.28"
    crio_conf:
      "crio":
        log_level: "info"
        log_dir: "/var/log/crio/pods"
      "crio.runtime":
        default_runtime: "runc"
        conmon: "/usr/bin/conmon"
        conmon_cgroup: "pod"
        default_capabilities: [
          "CHOWN", "DAC_OVERRIDE", "FSETID", "FOWNER",
          "SETGID", "SETUID", "SETPCAP", "NET_BIND_SERVICE", "KILL"
        ]
      "crio.image":
        default_transport: "docker://"
        pause_image: "registry.k8s.io/pause:3.9"
      "crio.network":
        network_dir: "/etc/cni/net.d/"
        plugin_dirs: ["/opt/cni/bin/"]
  roles:
    - ktooi.crio
```

## Testing

This role includes comprehensive Molecule tests to ensure compatibility across all supported operating systems and proper CRI-O functionality.

### Quick Start

```bash
# Install test dependencies
pip install molecule[docker] ansible-core

# Run all tests with default OS (Ubuntu 22.04)
molecule test

# Test specific OS distribution
MOLECULE_DISTRO=rockylinux8 molecule test
```

### Supported Test Distributions

- **Red Hat Family**: `rockylinux8`, `rockylinux9`, `fedora38`, `fedora39`
- **Debian Family**: `debian10`, `debian11`, `debian12`, `ubuntu2004`, `ubuntu2204`

### Test Coverage

Our tests verify:

- ✅ **Service Management**: CRI-O service installation, enablement, and startup
- ✅ **Configuration**: Proper configuration file generation and content
- ✅ **System Setup**: Kernel modules availability and system parameters
- ✅ **Binary Installation**: CRI-O binary presence and functionality
- ✅ **Version Compatibility**: CRI-O version verification

### Continuous Integration

All tests run automatically in GitHub Actions across the full OS matrix for every pull request and main branch push.

### Detailed Testing Documentation

For comprehensive testing information, development guidelines, and troubleshooting, see [molecule/README.md](molecule/README.md).

## Contributing

We welcome contributions! Please follow these guidelines:

### Development Workflow

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. **Run tests locally**: `molecule test` (test multiple OS with `MOLECULE_DISTRO=<distro>`)
5. Commit your changes (`git commit -m 'Add some amazing feature'`)
6. Push to the branch (`git push origin feature/amazing-feature`)
7. Open a Pull Request

### Testing Requirements

- All new features must include appropriate tests
- Tests must pass across all supported operating systems
- Follow existing test patterns and documentation standards
- Update documentation when adding new functionality

### Code Standards

- Follow Ansible best practices and conventions
- Use meaningful variable names and task descriptions
- Ensure idempotency for all tasks
- Add appropriate tags for Docker environment limitations

For detailed testing information and development guidelines, see [molecule/README.md](molecule/README.md).

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for a list of changes and version history.

## Authors

* **Kodai Tooi** - [GitHub](https://github.com/ktooi) | [Qiita](https://qiita.com/ktooi)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
