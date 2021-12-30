# Linuxfabrik KeePass Module for Ansible

## Installation

* This Ansible module needs the PyKeePass Module for Python.
  On Fedora, install it using `dnf install python-pykeepass` for example.
* Clone the Linuxfabrik KeePass Module for Ansible into one of those directories using `git@git.linuxfabrik.ch:linuxfabrik/ansible-module-keepass.git`:
  - any directory added to the `ANSIBLE_LIBRARY` environment variable (`$ANSIBLE_LIBRARY` takes a colon-separated list like `$PATH`)
  - `~/.ansible/plugins/modules/`
  - `/usr/share/ansible/plugins/modules/`


## Create a KeePass Database

Create a new, empty `.kdbx` file using KeePass, [KeeWeb](https://keeweb.info/) or similar. You can either use just a password, or both a password and keyfile.

In this example, we use `/tmp/vault.kdbx` with 'password' as the password and `/tmp/vault.key` as the keyfile. In real life, don't use the `/tmp` directory for this of course. ;-)


## How to use the Module in a Playbook

The next step in using the Linuxfabrik KeePass Module for Ansible is to consume it with an Ansible playbook.

* Create a playbook in any directory: `$ touch keepass.yml`
* Add the following to the new playbook file:

```yaml
- name: Test the KeePass Module
  hosts: localhost
  tasks:
  - name: Create a new password, or get the existing item
    keepass:
      database: /tmp/vault.kdbx
      hostname: dbhost01.localdomain
      keyfile: /tmp/vault.key
      password: 'password'
      purpose: MariaDB
      username: mariadb-admin
    register: creds
  - debug:
      msg: "Username: {{ creds.username }}, Password: {{ creds.password }}, New password: {{ creds.changed }}"

  - name: Create a longer new password, or get the existing item
    keepass:
      database: /tmp/vault.kdbx
      hostname: "{{ inventory_hostname }}"
      password: 'password'
      keyfile: /tmp/vault.key
      password_length: 45
      purpose: MariaDB
      username: mariadb-admin
    register: creds
  - debug:
      msg: "Username: {{ creds.username }}, Password: {{ creds.password }}, New password: {{ creds.changed }}"

  - name: Create a new, host-independent password, or get the existing item
    keepass:
      database: /tmp/vault.kdbx
      password: 'password'
      keyfile: /tmp/vault.key
      password_length: 45
      purpose: MariaDB
      username: mariadb-admin
    register: creds
  - debug:
      msg: "Username: {{ creds.username }}, Password: {{ creds.password }}, New password: {{ creds.changed }}"

```

* Run the playbook and analyze the output: 
  `$ ansible-playbook ./keepass.yml`


## Full Documentation

You can see the full documentation of the available parameters using

```bash
ansible-doc keepass
```

