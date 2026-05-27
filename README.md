# Inception Of Things

*Ce projet a été réalisé dans le cadre du cursus 42 par jmougel, klombard et mmorot.*

## Description

Le but de ce projet est de découvrir Kubernetes à travers une approche progressive en utilisant **K3s**, **K3d**, **Vagrant**, **Argo CD** et **Helm**.

Il est divisé en trois parties principales et une partie bonus :

| Parties | Description |
|---------|-------------|
| **P1** | Construire et approvisionner un cluster K3s à 2 nœuds avec Vagrant. |
| **P2** | Déployer plusieurs applications à l’aide de manifests Kubernetes. |
| **P3** | Utiliser K3d et Argo CD pour découvrir le déploiement continu. |
| **Bonus** | GitLab local, Argo CD et Helm sur K3d. |

## Stack technique

* **Langages :** Bash, YAML
* **Outils :** K3s, K3d, Docker, Vagrant, Argo CD, Helm

## Parties du projet

* [P1 : Construire un cluster K3s à 2 nœuds avec Vagrant](./p1/README.md)
* [P2 : Déployer des applications avec des manifests Kubernetes](./p2/README.md)
* [P3 : Utiliser K3d et Argo CD pour le déploiement continu](./p3/README.md)
* [Bonus : GitLab local, Argo CD et Helm sur K3d](./bonus/README.md)

Chaque partie introduit de nouveaux concepts et outils liés à l’orchestration de conteneurs et à l’automatisation du déploiement.

## Installation

```bash
git clone https://github.com/jasonmgl/InceptionOfThings
cd InceptionOfThings
```

Réfère-toi au README de chaque partie pour connaître sa configuration et ses prérequis spécifiques.

## Structure du projet

```text
InceptionOfThings
├── p1/
├── p2/
├── p3/
├── bonus/
├── .gitignore
└── README.md
```

## Auteur

* **Login :** jmougel
* **GitHub :** [jasonmgl](https://github.com/jasonmgl)