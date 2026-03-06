---
title: 02-Ansible-Playbooks
description: Playbooks Ansible
author: LButet / AtooSystem
date: 2026-03-06
draft: false
tags:
  - IAC
  - Ansible
---

# Playbooks Ansible

!!! Objectifs:
    - Configurer l'accès à un utilisateur dédié
    - Comprendre le fonctionnement
      - du fichier inventaire
      - du fichier ansible.cfg

!!! warning Pré-requis
  - **Niveau :** Débutant professionnel  
  - **Durée estimée :** 45 à 60 minutes  (+/- 15 minutes)
  - **Prérequis :** 
    - Ansible installé
    - Accès SSH à un nœud Debian/Ubuntu

## Récupérer les fichiers du tp
- **SUR ANS-MASTER UNIQUEMENT**
  - Récupérer le fichier
  - `ansible localhost -m get_url -a "url=https://raw.githubusercontent.com/atoosystem/ansible/refs/heads/main/ressources/debian.yml dest=~/ansible-labo/debian.yml"`
  - Lire ce fichier → que fait-il ?
  - `ansible-playbook debian.yml --limit localhost --check`
  - `ansible-playbook debian.yml --limit localhost --check -v`
  - `ansible-playbook debian.yml --limit localhost --check -vv` 
  - `ansible-playbook debian.yml --limit localhost`
  - Exécuter le playbook sur tous les nodes
  - Modifier le playbook:
    - Ajouter un play avec des paquets interdits
    - Ajouter un play avec des utiliser interdits
    - Tester / Amusez-vous

## BONUS:

> **FAIRE UN SNAPSHOT DE VOTRE VM AVANT**

Vous devez automatiser la sécurisation d'un serveur Debian fraîchement installé.
Le cahier des charges demande :

1. Désactiver l'authentification SSH par mot de passe
1. Désactiver le login root en SSH
1. Changer le port SSH 
   - Autoriser SSH port 22 pour contrôleur Ansible
   - TOUS les autres SSH en 2222
2. Activer UFW et autoriser uniquement les ports nécessaires

>  Utiliser les modules `lineinfile` et `blockinfile`, `ufw`, `service`  

**Consignes:**
  1. Tester avant d'appliquer
  ```bash
  ansible-playbook secure-ssh.yml --check --diff
  ```

> ⚠️ Le mode `--check` ne modifie rien. Il simule l'exécution et affiche les changements prévus.

  2. Appliquer le playbook
  ```bash
  ansible-playbook secure-ssh.yml
  ```
  1. Vérifier le résultat **Depuis le contrôleur Ansible :**
  ```bash
  # Vérifier que le port 2222 répond
  ansible serveurs -m wait_for -a "port=2222 timeout=10"

  # Vérifier le statut d'UFW
  ansible serveurs -m command -a "ufw status verbose" --become

  # Vérifier la config SSH
  ansible serveurs -m command -a "grep -E 'Port|PermitRootLogin|PasswordAuthentication' /etc/ssh/sshd_config"
  ```
