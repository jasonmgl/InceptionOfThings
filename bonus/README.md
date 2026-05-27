# Bonus - GitLab local, Argo CD et Helm sur K3d

*Ce projet a été réalisé dans le cadre du cursus 42 par jmougel, klombard et mmorot.*

[Retour](../README.md)

## Description

La partie bonus de ce projet consiste à reproduire l’environnement de la [Partie 3](../p3/README.md), mais cette fois en utilisant une instance **GitLab locale** au lieu d’un dépôt GitHub public.

Cette partie introduit également **Helm**, qui peut être utilisé pour simplifier l’installation et la mise à jour d’applications sur Kubernetes.

**Helm** est un gestionnaire de paquets pour Kubernetes. Il simplifie l’installation, la mise à jour et la suppression d’applications en générant les manifests nécessaires et en permettant de personnaliser les valeurs des charts via un fichier `values.yaml`.

La première étape consiste à créer un cluster **K3d**, qui exécute un cluster **K3s** à l’intérieur de conteneurs Docker et évite ainsi d’avoir recours à une machine virtuelle. Ensuite, trois namespaces doivent être créés :

* **argocd**, qui contient Argo CD
* **gitlab**, qui contient GitLab
* **dev**, qui contient l’application surveillée et déployée par Argo CD depuis un dépôt GitLab local

**MinIO** est utilisé comme service de stockage objet compatible S3, requis par l’installation locale de GitLab.

L’objectif est de vérifier qu’Argo CD synchronise automatiquement l’état du cluster avec les manifests Kubernetes stockés dans le dépôt GitLab.  
Lorsque la définition du Deployment est mise à jour dans le dépôt, l’application est automatiquement mise à jour dans le cluster.

### Types de manifests utilisés

* **Deployment** : utilisé pour déployer et gérer une application en créant et en maintenant des ReplicaSets et des Pods.
* **Service** : utilisé pour fournir un point d’accès réseau stable à un ensemble de Pods et rendre l’application accessible depuis l’intérieur du cluster.
* **Ingress** : utilisé pour router les requêtes HTTP externes vers le Service approprié selon l’hôte demandé.
* **Namespace** : utilisé pour organiser et séparer logiquement les ressources à l’intérieur d’un cluster Kubernetes. Il sert principalement à l’organisation plutôt qu’à une isolation réseau complète, puisque les applications de différents namespaces peuvent toujours communiquer entre elles par défaut.

Les images Docker utilisées pour cet exercice sont :

* `wil42/playground:v1`
* `wil42/playground:v2`

## Contraintes

* Créer trois namespaces :
  * Le premier doit être dédié à Argo CD.
  * Le second doit être dédié à GitLab.
  * Le troisième doit s’appeler **dev** et contenir une application déployée automatiquement par Argo CD depuis votre dépôt GitLab local.
* Vous pouvez utiliser l’application préconstruite fournie par Wil, disponible sur Docker Hub.
* Vous devez être capable de modifier la version de l’application depuis votre dépôt GitLab et de vérifier qu’elle a bien été mise à jour.

## Stack technique

* **Langages :** Bash, YAML
* **Outils :** K3d, Docker, Argo CD, Helm, GitLab

## Instructions

### Installation

```bash
git clone https://github.com/jasonmgl/InceptionOfThings
cd InceptionOfThings/bonus
make up
```

Assurez-vous que les outils suivants sont installés sur votre système :

* Make

Assurez-vous que les entrées suivantes sont présentes dans votre fichier `/etc/hosts` :

| Adresse IP | Hôtes |
|------------|-------|
| `127.0.0.1` | `argocd.local`, `gitlab.local`, `minio.local`, `jmougel.local` |

Un fichier `.env.sample` est fourni pour vous aider à personnaliser l’environnement local.

### Utilisation

Un **Makefile** est fourni pour faciliter l’exécution du projet. Les commandes suivantes sont disponibles :

| Commande | Description |
|---------|-------------|
| `make re` | Exécute `make purge` puis `make up`. |
| `make up` | Installe l’environnement nécessaire au projet et démarre le cluster K3d. |
| `make purge` | Supprime complètement les outils installés par le script de configuration et désinstalle Docker de la machine hôte. Utilisez cette commande avec précaution. |
| `make help` | Affiche la liste des commandes disponibles. |

### Dashboards et accès

Tous les services sont exposés localement à travers le même point d’entrée Ingress sur le port `8888`, en utilisant un routage basé sur l’hôte.

Rendez-vous aux adresses suivantes pour accéder aux services :

| Service | URL | Description |
|---------|-----|-------------|
| Argo CD | `http://argocd.local:8888/` | Accéder au dashboard Argo CD. |
| GitLab | `http://gitlab.local:8888/` | Accéder au dashboard GitLab. |
| Application | `http://jmougel.local:8888/` | Accéder à l’application surveillée par Argo CD. |
| MinIO | `http://minio.local:8888/` | Accéder au dashboard MinIO. |

### Identifiants

À des fins de démonstration, les identifiants par défaut utilisés dans ce projet sont :

* **Argo CD** — `admin` / `adminadmin`
* **GitLab** — `root` / `Mmorot1234@`
* **MinIO** — `admin` / `adminadmin`

## Validation

Vous pouvez vérifier que l’environnement fonctionne correctement avec :

```bash
kubectl get namespaces
kubectl get pods -n argocd
kubectl get pods -n gitlab
kubectl get pods -n dev
kubectl get ingress -A
```

Résultat attendu :

- Les namespaces `argocd`, `gitlab` et `dev` doivent exister.
- Tous les Pods du namespace `argocd` doivent être dans l’état `Running`.
- Tous les Pods nécessaires dans le namespace `gitlab` doivent être créés et fonctionner correctement.
- Le Pod de l’application dans le namespace `dev` doit être dans l’état `Running`.
- Les ressources Ingress doivent être présentes et correctement exposées.
- Le dashboard Argo CD doit être accessible à l’adresse `http://argocd.local:8888/`.
- Le dashboard GitLab doit être accessible à l’adresse `http://gitlab.local:8888/`.
- L’application déployée par Argo CD doit être accessible à l’adresse `http://jmougel.local:8888/`.
- Le dashboard MinIO doit être accessible à l’adresse `http://minio.local:8888/`.

## Structure du projet

```text
bonus
├── Makefile
├── README.md
├── .env.sample
├── scripts
│   ├── get-docker.sh
│   ├── install.sh
│   ├── uninstall.sh
│   └── remove-docker.sh
└── confs
    ├── argocd
    │   ├── argocd-app.yaml
    │   ├── ingress.yaml
    │   └── namespace.yaml
    ├── gitlab
    │   ├── values.yaml
    │   └── namespace.yaml
    └── k3d-config.yaml
```

## Ressources

### Images

![Structure du projet K3d](https://i.postimg.cc/7PVdqGrF/Screenshot-from-2026-04-10-17-26-47.png)
![Structure K3d](https://tse3.mm.bing.net/th/id/OIP.7MD59m547aIA46rnCg4w5gHaDf?pid=Api)

### Articles

* [Helm install : déployer et gérer vos releases](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/helm/install-releases/#_top)
* [Helm en 15 minutes : installer et déployer votre premier chart](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/helm/introduction/)
* [Repos Helm : ajouter, rechercher et inspecter des charts](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/helm/repos-charts/)
* [Values Helm : personnaliser vos charts proprement avec -f et --set](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/helm/values/)
* [MinIO : stockage objet compatible S3](https://blog.stephane-robert.info/docs/services/stockage/minio/)

## Utilisation de l’IA

J’ai principalement utilisé l’IA pour m’aider à comprendre certains concepts, générer des schémas et créer des quiz.

## Auteur

* **Login :** jmougel
* **GitHub :** [jasonmgl](https://github.com/jasonmgl)

[Retour](../README.md)