[![Build Status](https://travis-ci.org/wahidsadik/ansible-role-harden-ubuntu.svg?branch=master)](https://travis-ci.org/wahidsadik/ansible-role-harden-ubuntu)

Role Name
=========

An Ansible role to harden Ubuntu 14.04 LTS and 16.04 LTS after boot up.

The role is available on Ansible Galaxy: [https://galaxy.ansible.com/wahidsadik/ansible-role-harden-ubuntu](https://galaxy.ansible.com/wahidsadik/ansible-role-harden-ubuntu).

To add this role from Ansible Galaxy, run: `ansible-galaxy install wahidsadik.ansible-role-harden-ubuntu`.

To add this from your Ansible `requirements.yml`, add this to the file:

    src: wahidsadik.ansible-role-harden-ubuntu

Warning!
> - All aspects are configurable.
> - The role will prevent login of `root` user.
> - The role will only allow login via SSH. You need to provide one or more public of machines you want to access from.
> - Some parts non-idempotent, hence rerunning may not work without proper toggle.

Requirements
------------

The target system must have `python2` installed on it. On Ubuntu 16.04, it may not be the case. See example 3 below to see how to fix that.

Role Variables
--------------

The role defines the following variables in `defaults/main.yml`:

    enable_auto_update: True
    enable_fail2ban: True
    enable_ufw: True

    add_deployment_user: True
    deployment_user: deployer
    deployment_group: deployer

    enable_stop_password_authentication: True
    enable_stop_root_login: True

Set the values of the first 4 variables to `False` disable those behavior. It is highly recommended that all flags should be used.

Users must pass the following parameters (i.e. variables):

TODO: Mention that `enable_stop_password_authentication: True` will impact remote login for `root` user; local SSH should be fine.

- `deployment_password`. Has to be encrypted password. See the below section on how to generate one.

#### Option 1: Generate it on the fly

See the first example for this.


#### Option 2: Pre-generate it

See the second example for this.

> Encrypted password, generated on a Linux box using:  
> `$ echo 'import crypt,getpass; print crypt.crypt(getpass.getpass(), "$6$AC3bdCF7")' | python -`
>
> Here, `$6$` represents SHA-512 algorithm, and `AC3bdCF7` is the salt.
> or, use mkpasswd which is available most linux systems; if not, install it from `whois` package, and then run:  
> `$ mkpasswd --method=SHA-512 # random salt`  
> `$ mkpasswd --method=SHA-512 --salt=AC3bdCF7 # fixed salt`
>
> **None of these works on a Mac.**

#### Option 3...n: Other possibilities

- Store hashed password in vault
- Store salt in vault
- Pass hashed password from CLI (and don't record the command :), etc.

- `public_keys`: List of filename(s) containing public keys of machines you want to access the new machine from. Define it like this:


    public_keys:
      - abc


Dependencies
------------

It is assumed that this role will be run right after creating a new machine. Hence, `root` or equivalent user with `root` equivalent permission has to drive this role.

Example Playbook
----------------

#### Example 1: Base example

    - hosts: all

      vars:
        # example uses on how to disable a variable
        - enable_ufw: False
      vars_prompt:
        - name: "deployment_password"
          prompt: "What password to use for new user?"
          private: yes
          encrypt: "sha512_crypt"
          confirm: yes
          salt_size: 7

      roles:
        - {
            role: wahidsadik.ansible-role-harden-ubuntu,
            public_keys: [
              '~/.ssh/id_rsa.pub'
            ]

          }

Assuming you saved this playbook as `test-hardening-role.yml`, run it like this:

- `$ ansible-playbook -i <my-ip>, test-hardening-role.yml --user=<connection-user> --ask-pass`. For password-based login. Enter user's password at prompt and then new user's password.
- `$ ansible-playbook -i <my-ip>, test-hardening-role.yml --user=<connection-user> --become --ask-become-pass`. For SSH-based login. Enter sudo user's password at prompt and then new user's password. 

#### Example 2: With non-root user and pre-configured password

    - hosts: servers
      vars:
        public_keys:
          - '~/.ssh/id_rsa.pub'
        # the password is 'test'
        deployment_password: $6$AC3bdCF7$MA5sPtsGsOei6fCtyyzHeOqBpEzsi.yl9wS1yaP1.nKhuNR6ZBmcouWh6XJkrFdzreENtvUF4Gr2R0gfIQ/PT.
      roles:
         - { 
             role: wahidsadik.ansible-role-harden-ubuntu,
           }

You can run it the same way it's shown in last example.

#### Example 3: Ready to use playbook

- Ubuntu 14.04: [ubuntu-14.04-hardening.yml](https://gist.github.com/wahidsadik/03396d9fb14b8342f26cd65792e1509c)
- Ubuntu 16.04: [ubuntu-16.04-hardening.yml](https://gist.github.com/wahidsadik/e0005717c783b426a69b7099385a44b7)


License
-------

MIT

Author Information
------------------

Wahid Sadik

Repo: [https://github.com/wahidsadik/ansible-role-harden-ubuntu](https://github.com/wahidsadik/ansible-role-harden-ubuntu).
