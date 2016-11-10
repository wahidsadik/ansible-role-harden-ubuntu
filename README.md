[![Build Status](https://travis-ci.org/wahidsadik/ansible-role-harden-ubuntu.svg?branch=master)](https://travis-ci.org/wahidsadik/ansible-role-harden-ubuntu)

Role Name
=========

An Ansible role to harden Ubuntu 14.04 LTS and 16.04 LTS after boot up.

The role is available on Ansible Galaxy: [https://galaxy.ansible.com/wahidsadik/ansible-role-harden-ubuntu](https://galaxy.ansible.com/wahidsadik/ansible-role-harden-ubuntu).

To add this role from Ansible Galaxy, run: `ansible-galaxy install wahidsadik.ansible-role-harden-ubuntu`.

To add this from your Ansible `requirements.yml`, add this to the file:

    src: wahidsadik.ansible-role-harden-ubuntu

Warning!
> - The role will prevent login of `root` user.
> - The role will only allow login via SSH. You need to provide one or more public of machines you want to access from.
> - Some parts non-idempotent, hence rerunning may not work.

Warning!
> The role current doesn't handle more that 1 public file. When provided with more than 1, it will put the last one.
>
> [ ] Fix it.

Requirements
------------

The target system must have `python2` installed on it. On Ubuntu 16.04, it may not be the case. See example 3 below to see how to fix that.

Role Variables
--------------

The role defines the following variables in `defaults/main.yml`:

    deployment_user: deployer
    deployment_group: deployer

Users must pass the following parameters (i.e. variables):

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

Example 1: Base example

    - hosts: all
      remote_user: root

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

- `ansible-playbook -i <my-ip>, test-hardening-role.yml --ask-pass`. Enter `root` user's password at prompt.
- `ansible-playbook -i <my-ip>, test-hardening-role.yml --user=ubuntu --ask-pass`. When your super user is something other than `root`, in this case it's `ubuntu`. Enter user's password at prompt.

Example 2: With non-root user and pre-configured password

    - hosts: servers
      vars:
        public_keys:
          - '~/.ssh/id_rsa.pub'
      roles:
         - { role: wahidsadik.ansible-role-harden-ubuntu,
             deployment_password: $6$AC3bdCF7$oDbt3TE2NHQmsWWHrK1hN17utmFtQJt00fV0N.xA664IyGDpHEJmZbGZ..b5J3ibyvXlbc7jN3VGh3lBt4dc5/
             # public_keys is passed from above
           }

You can run it the same way it's shown in last example.

Example 3: Playbook for Ubuntu 16.04 LTS

You will need special configuration for Ubuntu 16.04. See this [gist](https://gist.github.com/wahidsadik/6f163c6eb7e286c3d19d378c42900c5a).

License
-------

MIT

Author Information
------------------

Wahid Sadik

Repo: [https://github.com/wahidsadik/ansible-role-harden-ubuntu](https://github.com/wahidsadik/ansible-role-harden-ubuntu).
