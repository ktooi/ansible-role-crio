# Molecule Testing for ansible-role-crio

This directory contains Molecule tests for the CRI-O Ansible role. Molecule is used to test the role across multiple operating systems and scenarios to ensure reliability and compatibility.

## Test Strategy

### Testing Philosophy

Our testing approach focuses on:

1. **Multi-OS Compatibility**: Testing across all supported operating systems
2. **Functional Verification**: Ensuring CRI-O is properly installed and configured
3. **Docker Environment**: Using Docker containers for fast, isolated testing
4. **CI/CD Integration**: Automated testing in GitHub Actions

### Test Scenarios

#### Default Scenario (`molecule/default/`)

The default scenario tests the basic installation and configuration of CRI-O:

- **Prepare Phase**: Minimal system preparation (apt cache update for Debian-based systems)
- **Converge Phase**: Execute the CRI-O role with default settings
- **Verify Phase**: Comprehensive validation of the installation

### Supported Operating Systems

The following operating systems are tested in our CI pipeline:

#### Red Hat Family
- **Rocky Linux 8**: RHEL 8 compatible distribution
- **Rocky Linux 9**: RHEL 9 compatible distribution
- **Fedora 38**: Latest stable Fedora
- **Fedora 39**: Latest Fedora release

#### Debian Family
- **Debian 10 (Buster)**: Stable Debian release
- **Debian 11 (Bullseye)**: Current stable Debian
- **Debian 12 (Bookworm)**: Latest Debian release
- **Ubuntu 20.04 LTS**: Long-term support Ubuntu
- **Ubuntu 22.04 LTS**: Latest LTS Ubuntu

### Test Coverage

#### Installation Verification
- ✅ CRI-O service is installed and enabled
- ✅ CRI-O binary is present and executable
- ✅ CRI-O configuration file is created
- ✅ CRI-O version command works correctly

#### System Configuration
- ✅ Required kernel modules (overlay, br_netfilter) are available
- ✅ Kernel parameters file is created with correct content
- ✅ Network bridge and IP forwarding parameters are set

#### Service Management
- ✅ CRI-O service can be started (when systemd is available)
- ✅ Service status verification
- ✅ Service enablement verification

### Docker Environment Considerations

#### Limitations in Docker Containers

Some tests are tagged with `molecule-notest` and skipped in Docker environments due to container limitations:

1. **Kernel Module Loading**: Docker containers share the host kernel
2. **Systemd Services**: Limited systemd functionality in containers
3. **Privileged Operations**: Some kernel operations require privileged access

#### Workarounds Implemented

- **Conditional Testing**: Tests that require privileged access are conditionally executed
- **Module Availability**: We test module availability rather than loading
- **Service Simulation**: Service tests are performed when systemd is available

### Configuration Details

#### Docker Container Setup

```yaml
platforms:
  - name: instance
    image: quay.io/ansible/molecule-${MOLECULE_DISTRO:-ubuntu2204}:latest
    command: /lib/systemd/systemd
    volumes:
      - /sys/fs/cgroup:/sys/fs/cgroup:rw
    cgroupns_mode: host
    privileged: true
    pre_build_image: true
```

#### OS-Specific Configurations

- **Rocky Linux/Fedora**: Use `su` for privilege escalation to avoid PAM issues
- **Ubuntu/Debian**: Use `/tmp` as remote temporary directory
- **All Systems**: Explicit Python interpreter configuration for consistency

## Running Tests Locally

### Prerequisites

```bash
pip install molecule[docker] ansible-core
```

### Basic Test Execution

```bash
# Run all tests with default OS (Ubuntu 22.04)
molecule test

# Test specific OS
MOLECULE_DISTRO=rockylinux8 molecule test

# Run only verification without full test cycle
molecule verify
```

### Available Distributions

- `ubuntu2004`
- `ubuntu2204`
- `debian10`
- `debian11`
- `debian12`
- `rockylinux8`
- `rockylinux9`
- `fedora38`
- `fedora39`

### Test Phases

1. **Dependency**: Install role dependencies
2. **Lint**: Run ansible-lint and yamllint
3. **Cleanup**: Remove existing test instances
4. **Destroy**: Ensure clean state
5. **Syntax**: Validate Ansible syntax
6. **Create**: Create test instances
7. **Prepare**: Run preparation playbook
8. **Converge**: Execute the role
9. **Idempotence**: Verify role idempotency
10. **Verify**: Run verification tests
11. **Cleanup**: Clean up test instances
12. **Destroy**: Remove test instances

## Continuous Integration

### GitHub Actions Integration

Tests are automatically executed in GitHub Actions for:

- **Pull Requests**: All supported OS combinations
- **Main Branch**: Full test matrix on push
- **Scheduled**: Weekly full test runs

### CI Configuration

The CI matrix includes all supported OS versions and uses:

- **Parallel Execution**: Multiple OS tests run simultaneously
- **Fail Fast**: Disabled to see all OS results
- **Timeout**: 45 minutes per job to handle slower systems

## Test Maintenance

### Adding New OS Support

1. Add the new OS to `.github/workflows/ci.yml` matrix
2. Create corresponding vars file in `vars/` directory
3. Update this documentation
4. Test locally before submitting PR

### Updating Tests

When modifying the role:

1. Update verification tests in `verify.yml` if new features are added
2. Consider Docker environment limitations
3. Test across multiple OS versions
4. Update documentation as needed

### Troubleshooting Common Issues

#### Docker Permission Issues
```bash
# Ensure Docker daemon is running and user has permissions
sudo usermod -aG docker $USER
newgrp docker
```

#### Systemd in Docker
```bash
# Some systemd operations may fail in Docker
# These are expected and handled by conditional tests
```

#### Network Issues
```bash
# Ensure Docker can access external repositories
docker run --rm -it ubuntu:22.04 apt-get update
```

## Best Practices

### Test Development

1. **Idempotency**: Ensure all tasks are idempotent
2. **Conditional Logic**: Use appropriate conditions for Docker limitations
3. **Error Handling**: Provide clear error messages in assertions
4. **Documentation**: Update tests documentation when adding new tests

### Performance Optimization

1. **Image Caching**: Use pre-built images when possible
2. **Parallel Testing**: Leverage CI parallelization
3. **Selective Testing**: Use tags to run specific test subsets

### Security Considerations

1. **Privileged Containers**: Only use when necessary
2. **Network Isolation**: Tests should not require external network access beyond package repositories
3. **Clean State**: Ensure tests start from a clean state

## Contributing

When contributing to tests:

1. Follow existing test patterns
2. Add appropriate tags for Docker limitations
3. Test across multiple OS versions
4. Update documentation
5. Ensure CI passes before submitting PR

For questions or issues with testing, please open an issue in the main repository.