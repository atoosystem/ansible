---
title: 00-Ansible-Installation 
description: Installer Ansible via pipx sous Debian.
author: LButet / AtooSystem
date: 2026-03-06
draft: false
tags:
  - IAC
  - Ansible
  - Debian
---

# Installer Ansible via pipx sous Debian

## Pré-requis:

> Disposer d'une Machine master pour ansible : 
- Example de configuration:
  - ans-master : 
    - Debian 12 / 13
  - 2 cartes:
    1. Bridge → Internet
    2. Lan-ansible → 172.16.60.101
  - Miroir: ftp.fr.debian.org
  - utilisateur dédié
    - ansible (sudoer)

## 📦 Installation 

- Bonne façon d'installer pipx sur Ubuntu/Debian
```bash
$ sudo apt update
$ sudo apt install pipx
$ pipx ensurepath
$ pipx install --include-deps ansible
``` 

## Vérification de la configuration
> Aucune erreur dans les résultats ci-dessous

```bash 
$ python3  - - version
$ pipx  - - version 
$ ansible  - - version
$ ansible -m ping localhost
```

## Tester les commandes 
- `ansible localhost -m ping`→ Expliquer:
- `ansible localhost -m command -a "uptime"`→ Expliquer:
- `ansible localhost -m setup | less`→ Expliquer:
- `ansible localhost -m setup -a "filter=distribution*"` → Expliquer:
- `ansible localhost -m file -a "path=~/ansible-labo state=directory"` → Expliquer:
- `ansible localhost -m copy -a "src=/etc/hosts dest=/tmp/hosts" --check` → Expliquer:
- `ansible localhost -m copy -a "dest=~/ansible-labo/ansible.cfg content='[defaults]\ninventory = ./hosts\nhost_key_checking = False\nprivate_key_file = ~/.ssh/ansible\ndeprecation_warnings = False\n#interpreter_python = auto_silent\ninterpreter_python = /usr/bin/python3\n'" --check` → Expliquer: