# Partie 3 - Utiliser K3d et Argo CD pour le déploiement continu

*Ce projet a été réalisé dans le cadre du cursus 42 par jmougel, klombard et mmorot.*

[Retour](../README.md)

## Description

La partie 3 de ce projet consiste à mettre en place un premier workflow de **déploiement continu** avec **Argo CD**.

La première étape consiste à créer un cluster **K3d**, qui exécute un cluster **K3s** à l’intérieur de conteneurs Docker et évite ainsi d’avoir recours à une machine virtuelle. Ensuite, deux namespaces doivent être créés :

* **argocd**, qui contient Argo CD
* **dev**, qui contient l’application surveillée et déployée par Argo CD depuis un dépôt GitHub

L’objectif est de vérifier qu’Argo CD synchronise automatiquement l’état du cluster avec les manifests Kubernetes stockés dans le dépôt GitHub.  
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

* Créer deux namespaces :
  * Le premier doit être dédié à Argo CD.
  * Le second doit s’appeler **dev** et contenir une application déployée automatiquement par Argo CD depuis votre dépôt GitHub public.
* Vous pouvez utiliser l’application préconstruite fournie par Wil, disponible sur Docker Hub.
* Vous devez être capable de modifier la version depuis votre dépôt GitHub public et de vérifier que l’application a bien été mise à jour.

## Stack technique

* **Langages :** Bash, YAML
* **Outils :** K3d, Docker, Argo CD

## Instructions

### Installation

```bash
git clone https://github.com/jasonmgl/InceptionOfThings
cd InceptionOfThings/p3
make up
```

Assurez-vous que les outils suivants sont installés sur votre système :

* Make

Assurez-vous que les entrées suivantes sont présentes dans votre fichier `/etc/hosts` :

| Adresse IP | Hôtes |
|------------|-------|
| `127.0.0.1` | `argocd.local`, `jmougel.local` |

Un fichier `.env.sample` est fourni pour vous aider à personnaliser l’environnement local.

### Utilisation

Un **Makefile** est fourni pour faciliter l’exécution du projet. Les commandes suivantes sont disponibles :

| Commande | Description |
|---------|-------------|
| `make re` | Exécute `make purge` puis `make up`. |
| `make up` | Installe l’environnement nécessaire au projet et démarre le cluster K3d. |
| `make purge` | Supprime complètement les outils installés par le script de configuration et désinstalle Docker de la machine hôte. Utilisez cette commande avec précaution. |
| `make help` | Affiche la liste des commandes disponibles. |

### Dashboard

Rendez-vous à l’adresse suivante pour accéder au dashboard Argo CD :

```text
http://argocd.local/
```

### Accès à l’application

Rendez-vous à l’adresse suivante pour accéder à l’application déployée par Argo CD :

```text
http://jmougel.local/
```

### Identifiants

À des fins de démonstration, les identifiants par défaut utilisés dans ce projet sont :

* **Argo CD** — `admin` / `adminadmin`

## Validation

Vous pouvez vérifier que l’environnement fonctionne correctement avec :

```bash
kubectl get namespaces
kubectl get pods -n argocd
kubectl get pods -n dev
kubectl get ingress -A
```

Résultat attendu :

- Les namespaces `argocd` et `dev` doivent exister.
- Tous les Pods dans le namespace `argocd` doivent être dans l’état `Running`.
- Le Pod de l’application dans le namespace `dev` doit être dans l’état `Running`.
- Les ressources Ingress doivent être présentes et correctement exposées.
- Le dashboard Argo CD doit être accessible à l’adresse `http://argocd.local/`.
- L’application déployée par Argo CD doit être accessible à l’adresse `http://jmougel.local/`.

## Structure du projet

```text
p3
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
    └── k3d-config.yaml
```

## Ressources

### Images

![Structure du projet K3d](https://i.postimg.cc/7PVdqGrF/Screenshot-from-2026-04-10-17-26-47.png)
![Structure K3d](https://tse3.mm.bing.net/th/id/OIP.7MD59m547aIA46rnCg4w5gHaDf?pid=Api)

### Articles

* [Créer des clusters Kubernetes locaux avec K3d](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/k3d/)
* [Namespaces Kubernetes : organiser et isoler logiquement vos ressources](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/namespaces/)
* [Documentation Argo CD](https://argo-cd.readthedocs.io/en/stable/)
* [Argo CD — Sécuriser votre déploiement GitOps](https://blog.stephane-robert.info/docs/pipeline-cicd/argocd/securiser/)
* [Installer Argo CD sur Kubernetes](https://blog.stephane-robert.info/docs/pipeline-cicd/argocd/installation/)
* [Argo CD — Déployer votre première application](https://blog.stephane-robert.info/docs/pipeline-cicd/argocd/premiere-application/)

### Vidéos

* [Namespaces and Contexts - #Kubernetes 13](https://www.youtube.com/watch?v=KthldM3Y4lg)

## Utilisation de l’IA

J’ai principalement utilisé l’IA pour m’aider à comprendre certains concepts, générer des schémas et créer des quiz.

## Auteur

* **Login :** jmougel
* **GitHub :** [jasonmgl](https://github.com/jasonmgl)

[Retour](../README.md)