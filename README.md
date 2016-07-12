[![Build Status](https://travis-ci.org/wahidsadik/ansible-role-harden-ubuntu.svg?branch=master)](https://travis-ci.org/wahidsadik/ansible-role-harden-ubuntu)

Role Name
=========

An Ansible role to harden Ubuntu 14.04 LTS after boot up.

The role is available on Ansible Galaxy: [https://galaxy.ansible.com/wahidsadik/ansible-role-harden-ubuntu](https://galaxy.ansible.com/wahidsadik/ansible-role-harden-ubuntu).

To add this role from Ansible Galaxy, run: `ansible-galaxy install wahidsadik.ansible-role-harden-ubuntu`.

To add this from your Ansible `requirements.yml`, add this to the file:

    src: wahidsadik.ansible-role-harden-ubuntu

Waring!
> - The role will prevent login of `root` user.
> - The role will only allow login via SSH. You need to provide one or more public of machines you want to access from.
> - Some parts non-idempotent, hence rerunning may not work.

Requirements
------------

None.

Role Variables
--------------

The role defines the following variables in `defaults/main.yml`:

    deployment_user: deployer
    deployment_group: deployer

Users must pass the following parameters (i.e. variables):

- `deployment_password`. Has to be encrypted password. See the below section on how to generate one.

> Encrypted password, generated on a Linux boxusing:  
> `$ echo 'import crypt,getpass; print crypt.crypt(getpass.getpass(), "$6$AC3bdCF7")' | python -`
>
> Here, `$6$` represents SHA-512 algo, and `AC3bdCF7` is the salt.
> or, use mkpasswd which is available most linux systems; if not, install it from `whois` package, and then issue  
> `$ mkpasswd --method=SHA-512 # random salt`
> `$ mkpasswd --method=SHA-512 --salt=AC3bdCF7 # fixed salt`
>
> **None of these works on a Mac.**


- `public_keys`: List of filename(s) containing public keys of machines you want to access the new machine from. Define it like this:

      public_keys:
        - abc
        - def

Dependencies
------------

It is assumed that this role will be run right after creating a new machine. Hence, `root` or equivalent user with `root` equivalent permission has to drive this role.

Example Playbook
----------------

TBD

Show examples of:

- With simplest possible variables


- With non-root user and in simplest possible variables
- With non-root user with full (and if needed other combinations) variable sets

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

    - hosts: servers
      vars:
        public_keys:
          - abc
          - def             
      roles:
         - { role: wahidsadik.ansible-role-harden-ubuntu,
             deployment_password: $6$AC3bdCF7$oDbt3TE2NHQmsWWHrK1hN17utmFtQJt00fV0N.xA664IyGDpHEJmZbGZ..b5J3ibyvXlbc7jN3VGh3lBt4dc5/,
             public_keys: public_keys
           }

^^ Try it out.

License
-------

MIT

Author Information
------------------

Wahid Sadik

Repo: [https://github.com/wahidsadik/ansible-role-harden-ubuntu](https://github.com/wahidsadik/ansible-role-harden-ubuntu).
