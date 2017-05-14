# mattermost-ansible
This is an Ansible Playbook that installs a standalone version of Mattermost, which is an open-source Slack alternative.
This playbook installs Mattermost version 3.5.1 (Team Edition) by default.

It downloads the binary from [mattermost.org](https://www.mattermost.org/download/). If you need to install the Enterprise
edition, consult the Mattermost documentation. This playbook installs
 * mysql - database server, with transparent encryption (tablespace encryption) enabled for mattermost tables. PostgreSQL does not support TDE without an enterprise license. 
 * `nginx` - Web Server (acts as a reverse proxy)
 * SSL certificates are automatically generated from the [letsencrypt](https://letsencrypt.org) project. A cron job is
 created that automatically renews the SSL certificates once a month.

---


This playbook currently works with
 UNDER DEVELOPMENT, starting with ubuntu 16.04

---
I have no reason to think that versions of these operating systems installed locally or by other providers will not work,
but I have not tested them. If you are going to run into an issue, it will probably be with the firewall, as DigitalOcean
has very permissive firewall rules by default. If you are having an issue, try opening port `80` and `443`.

### Distro Specific Instructions/Info
#### Ubuntu 16.04
Ubuntu 16.04 does not ship with a system python installed (why this is true, I don't think I will ever understand.) You must
install it manually with `apt-get install python` before you run the playbook.
#### RHEL 6.8
The playbook completely disables the `iptables` firewall. This is not strictly necessary, but I hate `iptables` with my
 whole body and I was tired of fighting with it. If you want or need it, you should re-enable it, but none of the required
 ports will be open. Even better, submit a PR that fixes this issue (so I don't have to do it).

---

## Usage
* Install ansible with your package manager of choice. Ansible can also be installed via `pip`. This playbook was written
and tested with Ansible 2.1.1. If you can, I would recommend running the most recent version of ansible.


* Clone this repository.

* Make sure that the server you are installing Mattermost is properly configured with a FQDN. You should also have root
 access via ssh. Reverse DNS should also be properly configured for letsencrypt to work. If you are using a cloud
 hosting provider, this should be trivial.


* Edit `play.yml` and change the `vars` to reflect any changes you may want to make for your system. This playbook does
not do a complete installation with full configuration of all of the Mattermost options, but rather installs it to the
point where you can edit the relevant settings from within the web browser.


* **You should *always* edit the email address and db_password fields.** 


* Create a `hosts` file in the project directory. It only needs to contain one line, which is the IP address of the server
you wish to install Mattermost on.

* Run `ansible-playbook play.yml -i hosts` from the top of level of the project directory.



* Navigate to the FQDN of your server in a web browser. Consult the Mattermost documentation for further configuration
options.

---

## Post-Install
If you are planning to use MatterMost for any length of time, you should probably change the location of the
data directory. A large volume of attached block storage would not be a bad idea. A working email server should also
be configured for email notifications and invites.  You can do most of this from within the browser without manually editing
configuration files.

Encryption requires that
(1) Tables are stored using innodb with file_per_table set.
(2) Use of MySQL Keyring. This activates the keyring_file plugin.

You can disable/enable encryption for tables by issuing ALTER TABLE ... ENCRYPTION= statement against mattermost.
To protect attached files, you will need to configure either full disk or folder encryption on the data folder.
---

### Current To-Do's
- install tasks for mysql\
- create task to issue alter table encrypt against mattermost db once tables are created.
- test against ubuntu 
