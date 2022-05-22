[![CI](https://github.com/ktooi/ansible-role-crio/workflows/CI/badge.svg)](https://github.com/ktooi/ansible-role-crio/actions?query=workflow%3ACI+branch%3Amain)

# Ansible Role: crio

RHEL/CentOS, Debian/Ubuntu サーバに CRI-O をインストールします。

この role では次のことを行います。

* Kernel module のロード。
* Kernel parameter の設定。
* CRI-O をインストール。

この role では次のことを行いません。

* CRI-O の設定。

## Requirements

この role には特別な要件はありません。

## Role Variables

```yaml
crio_version: "1.23"
```

インストールする CRI-O のバージョンを指定します。
指定できる形式は "X.Y" 形式か、 "X.Y.Z" 形式のどちらかです。
`*` を含めて指定することはできません。

## Dependencies

None.

## Example Playbook

```yaml
- hosts: crio-servers
  roles:
    - ktooi.crio
```

## Authors

* **Kodai Tooi** [GitHub](https://github.com/ktooi), [Qiita](https://qiita.com/ktooi)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
