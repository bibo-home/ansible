
## 0. Installing ansible

```bash
pip install ansible
```

## 1. Simple yaml

```bash
ansible-playbook install_ubuntu_20.04.yml --ask-become-pass
```

## 2. Install for group of servers

```bash
ansible-playbook -i inventory.ini install_minimal.yml
```