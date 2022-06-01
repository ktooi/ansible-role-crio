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
crio_version: "1.24"
```

インストールする CRI-O のバージョンを指定します。
指定できる形式は "X.Y" 形式か、 "X.Y.Z" 形式のどちらかです。
`*` を含めて指定することはできません。

```yaml
crio_sysctl_filename: "/etc/sysctl.d/99-kubernetes-cri.conf"
```

Kernel parameter の設定を保存するファイル名を指定します。

```yaml
crio_os: Debian_10
```

インストールするホストの OS を指定します。

OS 毎に指定するべき値は [cri-o のインストール手順](https://cri-o.io/) に記載されている `$OS` の値になりますが、2022年5月現在リンク先の情報は古いように見受けられます。
私が調べたところ、現在有効な OS の指定は次の通りになります。

|Operating system|`$OS` (`crio_os`)|
|---|---|
|CentOS 7|CentOS_7|
|CentOS 8|CentOS_8|
|CentOS 8 Stream|CentOS_8_Stream|
|Debian 10|Debian_10|
|Debian 11|Debian_11|
|Fedora 35|Fedora_35|
|Fedora Rawhide|Fedora_Rawhide|
|Raspbian 10|Raspbian_10|
|Raspbian 11|Raspbian_11|
|Ubuntu 18.04|xUbuntu_18.04|
|Ubuntu 20.04|xUbuntu_20.04|
|Ubuntu 21.10|xUbuntu_21.10|
|Ubuntu 22.04|xUbuntu_22.04|

なお、有効な OS は CRI-O のバージョンによって異なるので、各バージョンごとに利用可能な OS は [openSUSE Download](https://download.opensuse.org/repositories/devel:/kubic:/libcontainers:/stable:/cri-o:/) からインストールしたい CRI-O のバージョンを選択して表示されるディレクトリ名を確認してください。

`vars/` 配下にファイルが格納されている OS の場合は、 `crio_os` は自動的に設定されるので指定する必要はありません。

```yaml
crio_version_url: '1.24'
```

CRI-O のリポジトリの URL の一部として利用するパラメータです。
`X.Y` もしくは `X.Y:/X.Y.Z` のどちらかの形式で指定されます。 (e.g., `1.24` もしくは `1.24:/1.24.0`)

`crio_version` の値から自動的に設定されるので指定する必要はありません。

```yaml
crio_long_version: '1.24'
```

CRI-O のバージョンを長い形式で指定するパラメータです。
`X.Y` もしくは `X.Y:X.Y.Z` のどちらかの形式で指定されます。 (e.g., `1.24` もしくは `1.24:1.24.0`)
`crio_version_url` と似たような形式になりますが、 `/` の有無が異なります。

`crio_version` の値から自動的に設定されるので指定する必要はありません。

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
