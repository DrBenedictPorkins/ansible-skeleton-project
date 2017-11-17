# Ansible Skeleton Project
This is a skeleton project for creating new ansible projects.


## Pre-requisites
1.  Python 2.7

## Project setup
1.  Install python module dependencies.
```
pip install -r ./extension/setup/python_requirements.txt
```
2.  Install external modules.  Note that if you do not have to include the --roles-path arg if it was not edited in ansible.cfg.
```
ansible-galaxy install --roles-path ./roles/external -r ./roles/requirements.yml
```
3.  Setup ansible vault password file.
  - store it in your profile.  The path is already referenced in the included ansible.cfg.
  ```
  echo '<password_for_vault>' >> ~/.ansible_vault_pass
  ```
4.  Run a playbook!  Example:
```
ansible-playbook -i inventories/test playbooks/test.yml --tags test
```


## Directory Layout
```
.
├── ansible.cfg                          # holds the ansible config
├── extension/
│   └── setup/                           # setup files for updating roles & dependencies
│       └── python_requirements.txt      # python module dependencies
├── inventories/                         # inventories for each environment.  Devlab environment listed as example.
│   └── test/
│       ├── group_vars/                  # variables of group scope
│       ├── hosts                        # complete inventory of environment
│       └── host_vars/                   # variables of host scope
├── keys/                                # ssh keys
├── playbooks/                           # individual playbooks for each tier
├── roles/                               
│   └── internal/                        # all roles created internally
│   └── external/                        # all roles in git or ansible galaxy
│   └── requirements.yml                 # information about external roles
└── site.yml                             # master playbook

```


## Managing roles
External roles should not be stored in our own git repository!  This makes the repo unruly and hard to update.  Separating the roles between internal and external allows it to be managed on the local machine or AWX.  It also makes it easy to discern between roles downloaded from external repos.

Ansible-galaxy, which comes bundled with ansible, should be used for installing the external roles.  It uses the file roles/requirements.yml to do this, and should define each role like below.  More info about managing roles is available in the [Ansible Galaxy Docs](http://docs.ansible.com/ansible/latest/galaxy.html).
```
---
# from galaxy
- src: ANXS.build-essential
  version: "v1.0.1"

# from git
- src: git+https://github.com/some/repo.git
  version: master
  name: some-ansible-role
```

## Variables
For consistency and ease of variable management, generally try to define variables in the following spaces:
- group_vars for variables that do not apply to a single host
- host_vars for variables that apply to a single host
- group_vars/all for top level variables that apply to an entire inventory


## Encrypted files
Some variables are sensitive and stored in encrypted ansible vault files.  See the [Ansible Vault Documentation](https://docs.ansible.com/ansible/latest/vault.html) for more details.

- creating encrypted vault files
```
ansible-vault create foo.yml
```
and editor will open where you can add data, and it will be saved when the editor is exited.
- editing existing encrypted files
```
ansible-vault edit foo.yml
```
- decrypt encrypted files
```
ansible-vault decrypt foo.yml
```
- encrypt a single string to embed in an unencrypted yaml file:
```
ansible-vault encrypt-string --vault-id <path_to_password_file> '<string_to_encrypt>' --name '<name_of_variable>'
```
