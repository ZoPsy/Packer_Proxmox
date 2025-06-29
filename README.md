# Packer_Proxmox
TP Packer - EPSI I2 EISI INFRA
# 💻 Déploiement automatisé de VMs avec Packer sur Proxmox

Ce projet contient la configuration Packer pour déployer automatiquement des machines virtuelles sur Proxmox, chacune dédiée à un rôle spécifique :

- 🖼️ Frontend (NGINX + Node.js)
- 🔧 Backend (Node.js)
- 🛢️ Database (MongoDB)

---

## 🎥 Démo vidéo : VM unique avec tous les services

> Pour des raisons de simplicité de démonstration, la vidéo montre un **déploiement unique** (1 VM avec NGINX + Node.js + MongoDB) via le dossier `MY_WORK`. 

```bash
cd /tp-packer

# Initialisation
packer init .

# Validation
packer validate -var-file=customdeb12.pkrvars.hcl

# Construction de la VM unique
packer build -var-file=customdeb12.pkrvars.hcl debian.pkr.hcl
```
Ce fichier installe tous les services sur une seule machine (pour la preuve de concept).

> https://youtu.be/9-wk6HrNyJM

➡️ Les fichiers sont testés et fonctionnels aussi en déploiement séparé (voir plus bas).

Déploiement modulaire : 3 VMs séparées
Les dossiers `FRONTEND/`, `BACKEND/`, et `DATABASE/` contiennent chacun un template Packer pour générer une VM dédiée.
## 1. FRONTEND
```bash
cd /tp-packer

# Initialisation
packer init .

# Validation
packer validate -var-file=FRONTEND/frontend.pkrvars.hcl

# Construction de la VM unique
packer build -var-file=FRONTEND/frontend.pkrvars.hcl FRONTEND/debian-frontend.pkr.hcl
```

## 2. BACKEND
```bash
cd /tp-packer

# Initialisation
packer init .

# Validation
packer validate -var-file=BACKEND/backend.pkrvars.hcl

# Construction de la VM unique
packer build -var-file=FRONTEND/backend.pkrvars.hcl FRONTEND/debian-backend.pkr.hcl
```

## 3. DATABASE
```bash
cd /tp-packer

# Initialisation
packer init .

# Validation
packer validate -var-file=DATABASE/database.pkrvars.hcl

# Construction de la VM unique
packer build -var-file=FRONTEND/database.pkrvars.hcl FRONTEND/debian-db.pkr.hcl
```

## Dossier commun `autoinstall/`
Tous les fichiers utilisent un fichier preseed.cfg placé dans :

```bash
autoinstall/preseed.cfg
```

Dans chaque fichier .pkr.hcl, la directive suivante permet à Packer de servir ce fichier au boot via HTTP :

```hcl
http_directory = "autoinstall"
```

⚠️ Important : exécutez toujours packer build depuis la racine du projet (et non depuis `FRONTEND/`, etc.), sinon le dossier `autoinstall` ne sera pas trouvé ou alors vous pouvez copier le dossier dans chaque partie `FRONTEND/`, etc.

## Adaptation des identifiants Proxmox
Avant d'exécuter les builds, vous devez adapter les variables suivantes dans chaque fichier *.pkrvars.hcl :
```hcl
api_username   = "root@pam"
api_password   = "votre_mot_de_passe"
proxmox_api_url = "https://IP:8006/api2/json"
proxmox_node    = "nom_de_votre_node"
```

## Accès à la VM
L'utilisateur packer est configuré automatiquement :

Login : packer
Mot de passe : packer123!

Droits sudo sans mot de passe
