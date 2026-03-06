---
title: 01-Ansible-Configuration
description: Configurer Ansible sous Debian.
author: LButet / AtooSystem
date: 2026-03-06
draft: false
tags:
  - IAC
  - Ansible
  - Debian
---

# Configurer Ansible sous Debian

!!! tldr Objectifs:
    - Configurer l'accès à un utilisateur dédié
    - Comprendre le fonctionnement
      - du fichier inventaire
      - du fichier ansible.cfg

## Architecture réseau du Laboratoire
> Adapter les adresses IP Si besoin  

| Host | IP V4 | Réseau labo
| ---
| ans-master| 172.16.60.101 | lan-ansible
| ans-node1 | 172.16.60.111 | lan-ansible
> Pas de gateway
> 
## Pré-requis:

- Disposer au-moins de deux machines sous Debian
  - **ans-master**: Maitre ansible
  - **ans-node1**:  Client à configurer 
  - Pour chaque machine:
    - Accès à internet
    - Pouvoir Mettre à jour et installer des paquets suivants
      ```bash
      $ apt update && apt upgrade
      $ apt install -y software-properties-common open-vm-tools wget sudo
      ```
    - Un utilisateur `ansible` dédié ( sudoer )
      - en root: `# usermod -aG sudo ansible`
      - connection ssh sans mot de passe
      ```bash
      $ sudo visudo
      # Ajouter la ligne suivante dans /etc/sudoers
      ansible ALL=(ALL:ALL) NOPASSWD: ALL
      ``` 

## Configuration utilisateur dédié 
- Sur la machine `Maître` ans-master:
  - Se connecter avec l'utilisateur `ansible` dédié
  - Ajouter l'ip des client(s) dans /etc/hosts, ou dans le DNS
  - Créer une paire de clefs SSH
  ```bash
  ansible@ans-master:~#
  ssh-keygen -t ed25519 -f ~/.ssh/ansible -C "ansible"
  ``` 
  - Copier la clef sur chaque `client`
    - `ssh-copy-id -i  ~/.ssh/ansible ansible@iphost`
  - Tester la connection ssh avec clef **pour chaque client**
    - `ssh -i ~/.ssh/ansible ansible@iphost`

## Projet ansible

## 📦 Configuration de ans-master
- Créer le dossier du projet **~/ansible-labo**
  1. Avoir une paire des Clés SSH
    ```bash
    # Génération clé (une seule fois)
    ssh-keygen -t ed25519 -C "votre-email@domaine.com"
    # Copie sur le serveur cible
    ssh-copy-id user@192.168.1.204
    ```
  2. Créer le fichier inventaire
  ```bash
  $ vi ~/ansible-labo/hosts
  localhost ansible_connection=local

  [dbserver]
  # ans-node2 ansible_host=192.168.1.10

  [webserver]
  ans-node1

  [debian:children]
  local
  webserver

  [all:vars]
  ansible_connection=ssh
  ansible_ssh_user=<votre user ansible>
  # ansible_ssh_pass=<votre mot de passe ansible>
  ```
  3. Créer le fichier de configuration ansible.cfg
  ```bash
  $ vi ~/ansible-labo/ansible.cfg
  [defaults]
  inventory = ./hosts         
  host_key_checking = False
  private_key_file = ~/.ssh/ansible
  deprecation_warnings = False
  #interpreter_python = auto_silent
  interpreter_python = /usr/bin/python3
  ```

## Tester les commandes 

1. Tester l'inventaire
   - `ansible -m ping all` → Expliquer:
   - `ansible-inventory -i hosts --list` → Expliquer:
   - `ansible -m ping local` → Expliquer:
   - `ansible -m ping webserver` → Expliquer:
   - `ansible -m ping debian` → Expliquer:
   - `ansible-inventory -i hosts --graph` → Expliquer:
   - `ansible -m ping all --list ` → Expliquer:
2. Gestion du système
   - `ansible webserver ommand -a "/sbin/reboot" -f 10` → Expliquer:
   - `ansible all -m command -a "uptime"` → Expliquer:
   - `ansible all -m service -a « name=ssh state=started » -b` → Expliquer:
   - `ansible localhost -a "/sbin/reboot" –b` → Expliquer:
3. Installation de paquets
   - `ansible all -m apt -a "name=wget state=present"` → Expliquer:
   - `ansible all -m apt -a "name=curl state=absent"` → Expliquer:
4. Gestion des fichiers avec Ansible
   - `ansible all -m copy -a "src=/etc/apt/sources.list dest=/etc/apt/sources.list.old" -b` → Expliquer:
   
## Liens utiles
- [Ansible Quickstart](https://docs.ansible.com/projects/ansible/latest/index.html)
- [Ansible CLI cheatsheet](https://docs.ansible.com/projects/ansible/latest/command_guide/cheatsheet.html)
