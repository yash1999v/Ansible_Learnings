# Ansible Notes

## Overview
- **Agentless**: No master-slave mode, just a controller and managed nodes.
- **Capabilities**:
  - Setup networks, databases, and configurations.
  - Automate security with Vault.

## Automating Linux Administration with Ansible
1. **Agentless**
2. **Cross-platform support** (SSH & WinRM)
3. **Human-readable** (YAML, plain text)
4. **Easy to version control**
5. **Dynamic inventory** (update inventory from inside or outside)
6. **Orchestration and integration** are easy
7. **From development to production**

## Ansible Architecture
- **Developed in Python** with rich plugin support:
  - **Module plugins**
  - **Inventory plugins**
  - **Connection plugins** (e.g., SSH connection plugin)
  - **Become plugins** (e.g., work as different users)
  - **Cache, lookup, vars, callback plugins**

## Ansible Components
- **Ansible Engine** → Ansible Core
- **Ansible Tower** → Deprecated, now **Ansible Automation Platform**
- **Installation on RHEL**:
  ```bash
  sudo dnf install -y ansible
  ```

## Configuration File (`ansible.cfg`)
Configuration files are applied in order of precedence:
1. `/etc/ansible.cfg` (system-wide, weakest)
2. `~/.ansible.cfg` (user-specific, overrides system-wide)
3. `./ansible.cfg` (project directory, stronger)
4. `ANSIBLE_CONFIG` environment variable (strongest)

### Generate default configuration:
```bash
ansible-config init --disabled > default_ansible.cfg
```
### View current configuration:
```bash
ansible-config dump
```

## Inventory Management
- Manage hosts from different sources (Satellite, Active Directory, etc.).
- Dynamic inventory using plugins.
- Example Inventory (`inventory.ini`):
  ```ini
  [webservers]
  servera
  serverb
  
  [dbservers]
  serverc
  serverd
  
  [rhel9]
  server[a:d]
  
  [rhel:children]
  rhel8
  rhel9
  ```
- Commands:
  ```bash
  ansible-inventory -i inventory.ini --graph   # Show inventory structure
  ansible-inventory -i inventory.ini --list    # Show inventory in list format
  ```
- Nested Groups Example:
  ```ini
  [usa]
  washington1.example.com
  washington2.example.com

  [canada]
  ontario01.example.com
  ontario02.example.com

  [north-america:children]
  canada
  usa
  ```

## YAML Playbooks
Playbooks contain multiple plays:
```yaml
---
- name: Configure important user consistently
  hosts: servera.lab.example.com
  tasks:
    - name: Ensure newbie user exists with UID 4000
      ansible.builtin.user:
        name: newbie
        uid: 4000
        state: present
```

## YAML Basics
- YAML Ain't Markup Language (Human-readable, serializable)
- Supports **key-value pairs**:
  ```yaml
  name: install httpd  # No quotes required
  ```
- **Indentation is important**
- Dictionary Example:
  ```yaml
  - name: Yashwanth
    email: yash@gmail.com
  ```
- **Syntax check for playbooks**:
  ```bash
  ansible-playbook --syntax-check playbook.yml
  ```

## Variables in Ansible
- Must start with an alphabet.
- **22 different ways to define variables** (Keep it simple).
- Example variable definition methods:
  - `roles/defaults/`
  - `inventory/group_vars/varsFile.yaml`
- Variable Precedence: [Docs](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#understanding-variable-precedence)

### Example Playbook Using Variables:
```yaml
---
- name: Printing my name
  hosts: localhost
  connection: local

  vars_files:
    - my_vars.yaml

  tasks:
    - name: Print name task
      ansible.builtin.debug:
        msg: "Hi {{ my_string }}"
```

### Example Using `set_fact` (Higher precedence):
```yaml
- name: Ensure fact controller learns about my vars
  ansible.builtin.set_fact:
    my_string: white  # Overrides any previous value
```

### Example Passing Variables from CLI:
```bash
ansible-playbook first.yaml -e my_string=Yashwanth -i inventory.ini
```

## Recommended Project Structure
```bash
project/
├── ansible.cfg
├── group_vars/
│   ├── datacenters
│   ├── datacenters1
│   ├── datacenters2
├── host_vars/
│   ├── demo1.example.com
│   ├── demo2.example.com
│   ├── demo3.example.com
│   ├── demo4.example.com
├── inventory
└── playbook.yml
```

## RHEL System Roles
System roles provide automation for system configuration:
- **Common roles**:
  - `linux-system-roles.vpn`
  - `linux-system-roles.firewall`
  - `linux-system-roles.timesync`
  - `linux-system-roles.sshd`
  - `linux-system-roles.certificate`
  - `linux-system-roles.storage`
- Default location: `/usr/share/ansible-roles`
- More details: [RHEL System Roles](https://access.redhat.com/articles/3050101)

## Ansible Modules & Plugins
- **Modules**: Rich module collection with plugins
- **Collections**: Combination of plugins, modules, and roles.
- **Ansible Galaxy**: Import/export collections.
- **Core modules**:
  ```yaml
  - ansible.builtin.dnf:
  - ansible.posix.firewalld:
  - ansible.builtin.copy:
  - ansible.builtin.debug:
  ```
- Commands:
  ```bash
  ansible-galaxy collection list  # Show installed collections
  ansible-doc -l                  # List available modules
  ansible-doc copy                 # Show details of copy module
  ```
## Ansible Content Collection
-https://console.redhat.com/ansible/automation-hub/
  to browse these collections.
- https://galaxy.ansible.com/
