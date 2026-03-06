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
    - Ajouter un play avec des utilisateurs interdits
    - Ajouter un play qui ajoute l'utilisateur `bob`
    - Ajouter un play qui met à jour tous les paquets
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
> ⚠️ Le mode `--syntax-check` ne modifie rien. Il vérifie juste la syntaxe.  
> ⚠️ Le mode `--check` ne modifie rien. Il simule l'exécution et affiche les changements prévus.
> ⚠️ Le mode `--diff` ne modifie rien. Il affiche les différences.


1. `ansible-playbook --syntax-check secure-ssh.yml` → Vérifier la syntaxe (sans connexion)
1. `ansible-playbook --check --diff secure-ssh.yml --limit ans-node1` → Simuler l'exécution (avec connexion)
1. `ansible-playbook secure-ssh.yml --limit ans-node1` → Appliquer réellement **SUR** ans-node1
1. Vérif



  1. Vérifier le résultat **Depuis le contrôleur Ansible :**
  ```bash
  # Vérifier que le port 2222 répond
  ansible serveurs -m wait_for -a "port=2222 timeout=10"

  # Vérifier le statut d'UFW
  ansible serveurs -m command -a "ufw status verbose" --become

  # Vérifier la config SSH
  ansible serveurs -m command -a "grep -E 'Port|PermitRootLogin|PasswordAuthentication' /etc/ssh/sshd_config"
  ```
