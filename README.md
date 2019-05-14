# fusiondirectory

This role sets up a FusionDirectory installation.
No data is written to your LDAP server, this has to be done by another role.
You need a PHP-capable webserver which serves `/usr/share/fusiondirectory/html`.

The schemas are not touched, [fusiondirectory_schemas](https://github.com/stuvusIT/fusiondirectory_schemas) is used for this.
Your LDAP server needs to have some schemas loaded, see the Requirements section for that.

## Requirements

Debian and an LDAP server needs to be configured and have the following schemas loaded:

```
cosine
core-fd
core-fd-conf
template-fd
ldapns
```

Note that some plugins may require more schemas.

## Role Variables

| Name                               | Default/Required   | Description                                                                                  |
|------------------------------------|:------------------:|----------------------------------------------------------------------------------------------|
| `fusiondirectory_plugin_packages`  | `[]`               | List of extra apt packages to install for FusionDirectory plugins                            |
| `fusiondirectory_http_group`       | `www-data`         | Group under which the web server runs. This group is allowed to read FusionDirectory's files |
| `fusiondirectory_force_ssl`        | `false`            | Force usage of SSL                                                                           |
| `fusiondirectory_ignore_acl`       |                    | Specification of a DN that ignores all FusionDirectory ACLs                                  |
| `fusiondirectory_logging`          | `true`             | Enable logging                                                                               |
| `fusiondirectory_debug_level`      | `0`                | Debug level for logging                                                                      |
| `fusiondirectory_display_errors`   | `false`            | Display errors in the FusionDirectory web interface                                          |
| `fusiondirectory_default_location` | :heavy_check_mark: | Location that is preselected in the dropdown at the login screen                             |
| `fusiondirectory_locations`        | :heavy_check_mark: | A list of locations. See the next section for details                                        |

#### Location specifications

| Name         | Default/Required   | Description                                                   |
|--------------|:------------------:|---------------------------------------------------------------|
| `name`       | :heavy_check_mark: | Name of this location                                         |
| `config`     |                    | LDAP base where FusionDirectory stores its configuration data |
| `debuglevel` |                    | Debuglevel of this location                                   |
| `referrals`  | :heavy_check_mark: | A list of referrals. See the next section for details         |

#### Referral specifications

| Name       | Default/Required   | Description                                  |
|------------|:------------------:|----------------------------------------------|
| `uri`      | :heavy_check_mark: | Full URI of this referral                    |
| `basedn`   | :heavy_check_mark: | Base DN of this referral                     |
| `admin`    | :heavy_check_mark: | DN with the permission to write LDAP entries |
| `password` | :heavy_check_mark: | Password of the DN in the previous variable  |
| `tls`      | `false`            | Use TLS for connecting                       |


## Example Playbook

```yml
- hosts: fd
  roles:
  - fusiondirectory
    fusiondirectory_default_location: ldap01
    fusiondirectory_locations:
      - name: ldap01
        referrals:
        - uri: "ldapi://%2Frun%2Fslapd%2Fldapi"
          basedn: "dc=example,dc=com"
          admin: "cn=root,dc=example,dc=com"
          password: water
    fusiondirectory_plugin_packages:
      - fusiondirectory-plugin-systems
      - fusiondirectory-plugin-mail
```

## License

This work is licensed under a [Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).

## Author Information

- [Janne Heß](https://github.com/dasJ)
