# Wildfly

[![Build Status](https://travis-ci.org/KAMI911/ansible-role-wildfly.svg?branch=master)](https://travis-ci.org/KAMI911/ansible-role-wildfly)

# Ansible Role: Installs Wildfly Java Application server (optionally with Hugepages)

Installs Wildfly Java Application server. Most complete Wildfly installation, supporting, init.d script, application naming, separated WIldfly distribuution and application.

## Table of Contents

1. [Requirements][Requirements]
2. [Installation][Installation]
3. [Role Variables][Role Variables]
4. [Dependencies][Dependencies]
5. [Example Playbook][Example Playbook]
6. [Licensing][Licensing]
7. [Author Information][Author Information]
8. [Support][Support]
9. [Admin User][Admin User]
10. [SSL Support][SSL Support]
11. [Contributing][Contributing]
12. [Donation][Donation]

## Requirements

None.

## Installation

    ansible-galaxy install kami911.wildfly

## Role Variables
It's important to change the bind addresses to localhost or internal network in
production environments. The management user is also intended for
non-production environments, so you must change these variables for production
or undefine them and the user creation task will be skipped. You can also set
the variable `wildfly_management_user_overwrite` to `no` to avoid the user
creation or override and have the correct change status.

Defaults:

    wildfly_version: 11.0.1.Final

    wildfly_user: wildfly
    wildfly_group: wildfly
    wildfly_mode: standalone
    wildfly_base_download_url: http://download.jboss.org/wildfly
    wildfly_name: wildfly-{{ wildfly_version }}
    wildfly_download_file: "{{ wildfly_name }}.tar.gz"
    wildfly_download_validate_certs: "yes"
    wildfly_download_url: "{{ wildfly_base_download_url }}/{{ wildfly_version }}/\
                        {{ wildfly_download_file }}"
    wildfly_download_dir: /tmp

    wildfly_install_dir: /opt
    wildfly_dir: "{{ wildfly_install_dir }}/{{ wildfly_name }}"

    wildfly_console_log_dir: "/var/log/wildfly"
    wildfly_console_log_file: "console.log"
    wildfly_console_log: "{{ wildfly_console_log_dir }}/\
                        {{ wildfly_console_log_file }}"

    wildfly_conf_dir: /etc/wildfly
    wildfly_config_file: standalone.xml
    wildfly_config_path: "{{ wildfly_dir }}/standalone/configuration/\
                                    {{ wildfly_standalone_config_file }}"
    wildfly_init_dir: /etc/init.d

    wildfly_bind_address: 0.0.0.0
    wildfly_management_bind_address: 0.0.0.0
    wildfly_manage_http_port: 9990
    wildfly_manage_https_port: 9993
    wildfly_http_port: 8080
    wildfly_https_port: 8443

    wildfly_enable_ssl: no
    wildfly_keystore_name: my.jks
    wildfly_keystore_path: "{{ wildfly_dir }}/standalone/configuration/\
                            {{ wildfly_keystore_name }}"
    wildfly_keystore_alias: my
    wildfly_keystore_password: "secret"
    wildfly_key_password: "secret"
    wildfly_application_ssl_identity: '
        <server-identities>
            <ssl>
                <keystore path="{{ wildfly_keystore_name }}"
                relative-to="jboss.server.config.dir"
                alias="{{ wildfly_keystore_alias }}"
                keystore-password="{{ wildfly_keystore_password }}"
                key-password="{{ wildfly_key_password }}"/>
            </ssl>
        </server-identities>'
    wildfly_https_listener: '
        <https-listener name="https-server" socket-binding="https"
        security-realm="ManagementRealm"/>'

    # Manually defined variables
    # wildfly_management_user: admin
    # wildfly_management_password: admin

## Dependencies

None.

## Example Playbook

    - hosts: all
      roles:
        - wildfly

## Licensing

The Wildfly Ansible role application and documantations are licensed under the terms of
the MIT / BSD, you will find a copy of this license in the
[LICENSE](LICENSE) file included in the source package.

## Author Information

This role was created in 2015 by Juan Diego Romero González, additional work in 2016-2018 by Kálmán Szalai - KAMI

## Support

If you have any question, do not hesitate and drop me a line.
If you found a bug, or have a feature request, you can [fill an issue](https://github.com/KAMI911/ansible-role-wildfly/issues).

## Admin User

It's recommended that you create Wildfly's admin user separately as follows:

    $ ansible-playbook main.yml --extra-vars "wildfly_management_user=admin wildfly_management_password=admin"

## SSL Support

In order to enable SSL for applications and the management interface you have
to set the `wildfly_enable_ssl` variable to `yes` and put the keystore file
into this role files folder.

You can create a self signed keystore file with the following command:

    $ keytool -genkey -alias mycert -keyalg RSA -sigalg MD5withRSA -keystore my.jks -storepass secret  -keypass secret -validity 9999

It's recommended that the first and last name is your hostname. After this file
is created, you have to set the keystore related variable in order to work
correctly.

To create a keystore with your own certificate you need to run the following commands:

    $ cat /etc/ssl/certs/ca-bundle.crt intermediate.crt > allcacerts.crt
    $ openssl verify -CAfile allcacerts.crt certificate.crt
    $ openssl pkcs12 -export -chain -CAfile allcacerts.crt -in certificate.crt -inkey private.key -out my.p12 -name my
    $ keytool -importkeystore -destkeystore my.jks -srckeystore my.p12 -srcstoretype pkcs12 -alias my

The first command will add your intermediate to the openssl's CAs; the order is
important and you may need to put the itermediate file before `ca-bundle.crt`.
The second command is to verify that your certificate is signed by a known CA,
usually if this step fails, the rest of the process will fail too.
The third command is to import the all our certificate files in pkcs12 format.
Finally we use keytool to create the keystore to be used in wildfly.

## Troubleshooting

  - If you're getting a `Connection refused` error in your browser check that
  you have the correct IP address in your `wildfly_bind_address` variable.

## Contributing

There are many ways to contribute to ansible-role-wildfly -- whether it be sending patches,
testing, reporting bugs, or reviewing and updating the documentation. Every
contribution is appreciated!

Please continue reading in the [contributing chapter](CONTRIBUTING.md).

### Fork me on Github

https://github.com/KAMI911/ansible-role-wildfly

Add a new remote `upstream` with this repository as value.

```
git remote add upstream https://github.com/KAMI911/ansible-role-wildfly.git
```

You can pull updates to your fork's master branch:

```
git fetch --all
git pull upstream HEAD
```

## Donation

If you find this useful, please consider a donation:

[![paypal](https://www.paypalobjects.com/en_US/i/btn/btn_donateCC_LG.gif)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=RLQZ58B26XSLA)

<!-- TOC URLs -->
[Requirements]: #requirements
[Installation]: #installation
[Role Variables]: #role_variables
[Dependencies]: #dependencies
[Example Playbook]: #example_playbook
[Licensing]: #licensing
[Author Information]: #author_information
[Support]: #support
[Admin User]: #admin_user
[SSL Support]: #ssl_support
[Contributing]: #contributing
[Donation]: #donation
