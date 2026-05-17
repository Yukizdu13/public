# FaceFusion Vast.ai Automation (installer_n_runner_facefusion_on_vast.ai)

## Description

Ce projet automatise l’installation et l’exécution de FaceFusion sur une instance Vast.ai afin de traiter des lots de vidéos avec accélération GPU CUDA.

Le workflow couvre :

- Installation de FaceFusion sur Vast.ai
- Configuration CUDA et dépendances
- Transfert des fichiers source/target
- Traitement batch automatisé
- Monitoring des outputs
- Vérification SHA256
- Téléchargement automatisé des résultats
- Fermeture optionnelle de l’instance Vast.ai

---

# Architecture générale

## Machine source

Contient :

```text
~/facefusion_files/
├── sources/
│   ├── 1.jpg
│   ├── 2.jpg
│   └── ...
├── targets/
│   ├── 1.mp4
│   ├── 2.mp4
│   └── ...
```

Les fichiers sont associés par numéro :

| Source | Target |
|---|---|
| 1.jpg | 1.mp4 |
| 2.jpg | 2.mp4 |

---

# Infrastructure Vast.ai

## Installation FaceFusion

Clone du dépôt :

```bash
git clone https://github.com/facefusion/facefusion.git /root/facefusion
```

Installation :

```bash
python3 install.py --onnxruntime cuda --skip-conda
```

Installation complémentaire CUDA :

```bash
pip install onnxruntime-gpu
```

---

# Compatibilité CUDA

Le système prévoit :

- CUDA 12
- CUDA 13
- création de symlinks si certaines libs sont absentes
- configuration dynamique de :

```bash
LD_LIBRARY_PATH
```

---

# Transfert des fichiers

Utilisation de :

```bash
scp
```

Destination Vast.ai :

```bash
/workspace/facefusion_files
```

---

# Exécution FaceFusion

## Processors

```text
face_swapper
face_enhancer
```

## Modèle

```text
hyperswap_1a_256
```

## Encodeur vidéo

```text
libx264
```

## Backend

```text
CUDA
```

---

# Script bash principal

## Fichier

```bash
/workspace/faceswaping.sh
```

## Fonctionnalités

- Détection automatique des GPU
- Un process FaceFusion par GPU
- Traitement parallèle
- Nettoyage `/tmp/facefusion`
- Logs détaillés
- Gestion des erreurs

---

# Script de monitoring

## Fichier

```bash
/workspace/checking_output.sh
```

## Fonctionnalités

- Détection des outputs terminés
- Calcul SHA256
- Génération des commandes SCP
- Validation des fichiers exportés

---

# Monitoring PowerShell

Depuis le poste Windows admin :

## Fonctionnalités

- Lecture des logs toutes les 15 secondes
- Téléchargement automatique SCP
- Vérification checksum
- Suppression des outputs validés
- Possibilité de shutdown Vast.ai via API


# Risques et problèmes connus

## CUDA

- incompatibilités CUDA 12/13
- librairies manquantes
- symlinks nécessaires

## SSH

- timeouts
- connexions interrompues
- SCP bloquant

## FaceFusion

- saturation VRAM
- erreurs ONNXRuntime
- corruption tmp

## Stockage

Attention au remplissage de :

```bash
/tmp/facefusion
```

---

# Permissions prévues

## Vast.ai

Autorisé :

- installation
- exécution scripts
- suppression outputs temporaires

Interdit :

- suppression système
- accès hors workspace

## Machine source

Autorisé :

- `scp`
- `ls`
- `sha256sum`

## Poste admin

Autorisé :

- génération script monitoring PowerShell
- monitoring
- API Vast.ai

---

# Recommandations

- Utiliser `tmux`
- Sauvegarder les logs
- Vérifier les checksums avant suppression
- Monitorer VRAM GPU
- Nettoyer `/tmp/facefusion` régulièrement

---

# Exemple de lancement

```bash
bash /workspace/faceswaping.sh
```

---

# Exemple de monitoring

```bash
bash /workspace/checking_output.sh
```

---

# Objectif final

Automatiser entièrement un pipeline FaceFusion GPU distribué sur Vast.ai avec :

- haute disponibilité
- reprise sur erreur
- transfert sécurisé
- vérification d’intégrité
- traitement PowerShell et Bash

# Crédits
https://www.e-kclt.fr
