#

```text
    _              _ _     _
   / \   _ __  ___(_| |__ | | ___
  / _ \ | '_ \/ __| | '_ \| |/ _ \
 / ___ \| | | \__ | | |_) | |  __/
/_/   \_|_| |_|___|_|_.__/|_|\___|
```

## Getting Started

```sh
# Create ansible ssh keys
cd ~/.ssh
ssh-keygen -t ed25519 -C "ansible"
# Enter file in which to save the key (/Users/<your username>/.ssh/id_ed25519): ansible

# Install ansible and vagrant
brew install ansible vagrant

# Run vagrant
vagrant up

# Destroy server when finished
vagrant destroy
```

## Ansible

is an extra-simple tool/framework/API for doing ‘remote things’. this command allows you to define and run a single task ‘playbook’ against a set of hosts

Using ad hoc commands is a quick way to run a single task on one or more managed nodes. 

Some examples of valid use cases are rebooting servers, copying files, checking connection status, managing packages, gathering facts, etc.

[Documentation](https://docs.ansible.com/ansible/latest/cli/ansible.html)

usage: ansible [-h] [--version] [-v] [-b] [--become-method BECOME_METHOD]
            [--become-user BECOME_USER]
            [-K | --become-password-file BECOME_PASSWORD_FILE]
            [-i INVENTORY] [--list-hosts] [-l SUBSET] [-P POLL_INTERVAL]
            [-B SECONDS] [-o] [-t TREE] [--private-key PRIVATE_KEY_FILE]
            [-u REMOTE_USER] [-c CONNECTION] [-T TIMEOUT]
            [--ssh-common-args SSH_COMMON_ARGS]
            [--sftp-extra-args SFTP_EXTRA_ARGS]
            [--scp-extra-args SCP_EXTRA_ARGS]
            [--ssh-extra-args SSH_EXTRA_ARGS]
            [-k | --connection-password-file CONNECTION_PASSWORD_FILE] [-C]
            [--syntax-check] [-D] [-e EXTRA_VARS] [--vault-id VAULT_IDS]
            [--ask-vault-password | --vault-password-file VAULT_PASSWORD_FILES]
            [-f FORKS] [-M MODULE_PATH] [--playbook-dir BASEDIR]
            [--task-timeout TASK_TIMEOUT] [-a MODULE_ARGS] [-m MODULE_NAME]
            pattern

```sh
# The pattern for ad hoc commands looks like this:
ansible [host-pattern] -m [module] -a “[module options]”

# host-pattern: the managed hosts to run against
# -m: the module to run
# -a: the list of arguments required by the module

# Examples
# Ping all hosts
ansible all -m ping

# Run a command on a particular host
ansible all --limit ansible-demo -a "/bin/echo hello"

# Copy a file to remote hosts
ansible all -m ansible.builtin.copy -a "src=./tmp/ dest=/tmp/hosts"
```

## Ansible Config

[Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-config.html)

usage: ansible-config [-h] [--version] [-v] {list,dump,view,init} ...

```sh
# Output a config
ansible-config init

# List available config options
ansible-config list

# List current config options
ansible-config view
```

## Ansible Playbook

Runs Ansible playbooks, executing the defined tasks on the targeted hosts.

[Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-playbook.html)

usage: ansible-playbook [-h] [--version] [-v] [--private-key PRIVATE_KEY_FILE]
                     [-u REMOTE_USER] [-c CONNECTION] [-T TIMEOUT]
                     [--ssh-common-args SSH_COMMON_ARGS]
                     [--sftp-extra-args SFTP_EXTRA_ARGS]
                     [--scp-extra-args SCP_EXTRA_ARGS]
                     [--ssh-extra-args SSH_EXTRA_ARGS]
                     [-k | --connection-password-file CONNECTION_PASSWORD_FILE]
                     [--force-handlers] [--flush-cache] [-b]
                     [--become-method BECOME_METHOD]
                     [--become-user BECOME_USER]
                     [-K | --become-password-file BECOME_PASSWORD_FILE]
                     [-t TAGS] [--skip-tags SKIP_TAGS] [-C]
                     [--syntax-check] [-D] [-i INVENTORY] [--list-hosts]
                     [-l SUBSET] [-e EXTRA_VARS] [--vault-id VAULT_IDS]
                     [--ask-vault-password | --vault-password-file VAULT_PASSWORD_FILES]
                     [-f FORKS] [-M MODULE_PATH] [--list-tasks]
                     [--list-tags] [--step] [--start-at-task START_AT_TASK]
                     playbook [playbook ...]

```sh
# Run a playbook
ansible-playbook bootstrap.yaml

# Run a playbook but pass vars
ansible-playbook bootstrap.yml --extra-vars "version=1.23.45 other_variable=foo"

# Install apache2 on the web servers
ansible-playbook ./playbooks/basic.yaml
ansible-playbook ./playbooks/advanced.yaml
```

## Ansible Pull

Used to pull a remote copy of ansible on each managed node, each set to run via cron and update playbook source via a source repository. This inverts the default push architecture of ansible into a pull architecture, which has near-limitless scaling potential.

None of the CLI tools are designed to run concurrently with themselves, you should use an external scheduler and/or locking to ensure there are no clashing operations.

The setup playbook can be tuned to change the cron frequency, logging locations, and parameters to ansible-pull. This is useful both for extreme scale-out as well as periodic remediation. Usage of the ‘fetch’ module to retrieve logs from ansible-pull runs would be an excellent way to gather and analyze remote logs from ansible-pull.

[Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-pull.html)

usage: ansible-pull [-h] [--version] [-v] [--private-key PRIVATE_KEY_FILE] [-u REMOTE_USER] [-c CONNECTION] [-T TIMEOUT]
                    [--ssh-common-args SSH_COMMON_ARGS] [--sftp-extra-args SFTP_EXTRA_ARGS] [--scp-extra-args SCP_EXTRA_ARGS]
                    [--ssh-extra-args SSH_EXTRA_ARGS] [-k | --connection-password-file CONNECTION_PASSWORD_FILE]
                    [--vault-id VAULT_IDS] [--ask-vault-password | --vault-password-file VAULT_PASSWORD_FILES]
                    [-e EXTRA_VARS] [-t TAGS] [--skip-tags SKIP_TAGS] [-i INVENTORY] [--list-hosts] [-l SUBSET]
                    [-M MODULE_PATH] [-K | --become-password-file BECOME_PASSWORD_FILE] [--purge] [-o] [-s SLEEP] [-f]
                    [-d DEST] [-U URL] [--full] [-C CHECKOUT] [--accept-host-key] [-m MODULE_NAME] [--verify-commit]
                    [--clean] [--track-subs] [--check] [--diff]
                    [playbook.yml ...]

```sh
# Basic ansible pull
ansible-pull -U https://github.com/dreamystify/<ansible-pull-repo>.git

# Advanced Ansible pull
ansible-pull -U https://github.com/Corey-Lylyk/ansible-laptop.git -C backend --key-file=~/.ssh/github -o local.yaml -e 'dept=backend'
```

## Ansible Console

A REPL that allows for running ad-hoc tasks against a chosen inventory from a nice shell with built-in tab completion (based on dominis’ ansible-shell).

[Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-console.html)

usage: ansible-console [-h] [--version] [-v] [-b]
                    [--become-method BECOME_METHOD]
                    [--become-user BECOME_USER]
                    [-K | --become-password-file BECOME_PASSWORD_FILE]
                    [-i INVENTORY] [--list-hosts] [-l SUBSET]
                    [--private-key PRIVATE_KEY_FILE] [-u REMOTE_USER]
                    [-c CONNECTION] [-T TIMEOUT]
                    [--ssh-common-args SSH_COMMON_ARGS]
                    [--sftp-extra-args SFTP_EXTRA_ARGS]
                    [--scp-extra-args SCP_EXTRA_ARGS]
                    [--ssh-extra-args SSH_EXTRA_ARGS]
                    [-k | --connection-password-file CONNECTION_PASSWORD_FILE]
                    [-C] [--syntax-check] [-D] [--vault-id VAULT_IDS]
                    [--ask-vault-password | --vault-password-file VAULT_PASSWORD_FILES]
                    [-f FORKS] [-M MODULE_PATH] [--playbook-dir BASEDIR]
                    [-e EXTRA_VARS] [--task-timeout TASK_TIMEOUT] [--step]
                    [pattern]

```sh
# Output a config
ansible-console <all?>

# Specify and inventory file
ansible-config -i inventory.json

# Example commands
ansible-console -i <inventory> --limit <optional-tag> --become -u root
```

```sh
# When in the console you can start typing and use tab completion
p
ping

# List will list all the hosts
root@all (1)[f:5]# list
ansible-demo

# Ping will ping all hosts
root@all (1)[f:5]# ping
ansible-demo | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

Further learning:

[Quick Intro to Console](https://www.linuxserver.io/blog/2018-02-09-a-quick-intro-to-ansible-console)

## Ansible Doc

displays information on modules installed in Ansible libraries. It displays a terse listing of plugins and their short descriptions, provides a printout of their DOCUMENTATION strings, and it can create a short “snippet” which can be pasted into a playbook.

[Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-doc.html)

usage: ansible-doc [-h] [--version] [-v] [-M MODULE_PATH]
                [--playbook-dir BASEDIR]
                [-t {become,cache,callback,cliconf,connection,httpapi,inventory,lookup,netconf,shell,vars,module,strategy,test,filter,role,keyword}]
                [-j] [-r ROLES_PATH]
                [-e ENTRY_POINT | -s | -F | -l | --metadata-dump]
                [--no-fail-on-errors]
                [plugin ...]

```sh
# Check for help
ansible-doc -h

# Check the docs
# For syntax, we have to provide the concerned module name to get all related documentation. Like below: –
ansible-doc <module name>

# Similarly, for plugins we have to use like below to get details of a plugin: –
ansible-doc –type <plugin type>

# Get the documentation for the copy command
ansible-doc copy

# Get a list of plugins
ansible-doc -t connection -l
ansible-doc -t lookup -s ansible.builtin.csvfile
```

Further learning:

[Ansible Doc Examples](https://www.educba.com/ansible-doc/)


## Ansible Vault

can encrypt any structured data file used by Ansible. This can include group_vars/ or host_vars/ inventory variables, variables loaded by include_vars or vars_files, or variable files passed on the ansible-playbook command line with -e @file.yml or -e @file.json. Role variables and defaults are also included!

Because Ansible tasks, handlers, and other objects are data, these can also be encrypted with vault. If you’d like to not expose what variables you are using, you can keep an individual task file entirely encrypted.

[Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-vault.html)

```sh
# Encrypt a file
ansible-vault create --vault-id @prompt secret.yaml

# Once the passphrase is entered, ansible vault encrypt file opens using default editor and we're able to put content into the file, as shown below
---
- name: This is a secret file
  hosts: server2.example.com
  tasks:
    - name: Execute command 'date'
      command: date

# Check the file
cat secret.yaml

# Decrypt a file to view
ansible-vault view --vault-id @prompt  secret.yaml

# Edit encrypted file
ansible-vault edit secret.yaml

# Use password file
echo "mypassword" > password_file

# Edit using the password file
ansible-vault edit --vault-id password_file secret.yaml
ansible-vault edit --vault-password-file password_file secret.yaml

# Encrypt existing file
ansible-vault encrypt --vault-id @prompt secret_conditonal.yaml

# Decrypt file
ansible-vault decrypt --ask-vault-pass secret.yaml

# Use vault in a playbook
ansible-playbook --vault-id @prompt secret.yaml
ansible-playbook secret.yaml --vault-password-file=password_file

# You can encrypt string within the playbook (refer to the 10 examples)
```

Further Learning:

[10 Examples](https://www.golinuxcloud.com/ansible-vault-example-encrypt-string-playbook/)

## Ansible Galaxy

Command to manage Ansible roles and collections.
Ansible Galaxy is a repository of Ansible roles.

[Documentation](https://docs.ansible.com/ansible/latest/cli/ansible-galaxy.html)

usage: ansible-galaxy [-h] [--version] [-v] TYPE ...

```sh
# Install mysql role
ansible-galaxy install geerlingguy.mysql
```

## Trouble shooting

```sh
# Login to vb
ssh root@192.168.56.2 -p 22 -i ~/.ssh/ansible -o "StrictHostKeyChecking=no"
```
