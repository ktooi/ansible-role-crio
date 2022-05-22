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

## Dependencies

None.

## Example Playbook

```yaml
- hosts: crio-servers
  roles: ktooi.crio.crio
    - ktooi.crio
````

## Authors

* **Kodai Tooi** [GitHub](https://github.com/ktooi), [Qiita](https://qiita.com/ktooi)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details


Translated with www.DeepL.com/Translator (free version)
