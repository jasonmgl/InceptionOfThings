# Partie 2 - Déployer des applications avec des manifests Kubernetes

*Ce projet a été réalisé dans le cadre du cursus 42 par jmougel, klombard et mmorot.*

[Retour](../README.md)

## Description

La partie 2 de ce projet consiste à créer, sur une machine virtuelle à l’aide de Vagrant, un cluster K3s à nœud unique hébergeant 3 applications web.

Chaque application affiche le nom du Pod sur lequel la requête est traitée.  
La deuxième application est déployée avec **3 réplicas** afin de répartir le trafic et d’améliorer la disponibilité.

### Types de manifests utilisés

* **Deployment** : utilisé pour déployer et gérer une application en créant et en maintenant des ReplicaSets et des Pods.
* **Service** : utilisé pour fournir un point d’accès réseau stable à un ensemble de Pods et rendre l’application accessible depuis l’intérieur du cluster.
* **Ingress** : utilisé pour router les requêtes HTTP externes vers le Service approprié selon l’hôte demandé.

L’image Docker utilisée pour cet exercice est :

* `paulbouwer/hello-kubernetes:1`

## Contraintes

* Une seule machine virtuelle avec la dernière version stable de la distribution de votre choix.
* K3s doit être installé en mode serveur.
* Mettre en place 3 applications web de votre choix.
* Les applications doivent être accessibles selon le **HOST** utilisé lors d’une requête vers l’adresse IP **192.168.56.110**.
* Le nom de cette machine doit être votre login suivi de **S**.
* Lorsqu’un client entre l’adresse IP **192.168.56.110** dans son navigateur avec l’hôte **app1.com**, le serveur doit afficher **app1**.
* Lorsque l’hôte **app2.com** est utilisé, le serveur doit afficher **app2**.
* Sinon, **app3** doit être sélectionnée par défaut.

## Stack technique

* **Langages :** Bash, YAML
* **Outils :** Vagrant, K3s

## Instructions

### Installation

```bash
git clone https://github.com/jasonmgl/InceptionOfThings
cd InceptionOfThings/p2
make up
```

Assurez-vous que les outils suivants sont installés sur votre système :

* Make
* Vagrant
* VirtualBox

Assurez-vous que les hôtes suivants sont présents dans votre fichier `/etc/hosts` :

| Adresse IP | Hôte |
|------------|------|
| `192.168.56.110` | `app1.local` |
| `192.168.56.110` | `app2.local` |
| `192.168.56.110` | `app3.local` |

### Utilisation

Un **Makefile** est fourni pour faciliter l’exécution du projet. Les commandes suivantes sont disponibles :

| Commande | Description |
|---------|-------------|
| `make up` | Démarre la machine virtuelle Vagrant. |
| `make down` | Arrête proprement la machine virtuelle Vagrant. |
| `make provision` | Réapprovisionne la machine virtuelle Vagrant si les scripts ont été mis à jour. |
| `make fclean` | Supprime la machine virtuelle Vagrant ainsi que ses fichiers de cache. |
| `make test` | Exécute des requêtes `curl` sur chaque application afin de vérifier que le routage basé sur l’hôte fonctionne correctement. |
| `make help` | Affiche la liste des commandes disponibles. |

### Validation

Exécutez les commandes suivantes pour vérifier que l’environnement fonctionne correctement :

```bash
kubectl get pods -o wide
kubectl get svc
kubectl get ingress
make test
```

Résultat attendu :

- Tous les Pods doivent être dans l’état `Running`.
- L’application `app2` doit avoir **3 réplicas en cours d’exécution**.
- Les Services de `app1`, `app2` et `app3` doivent être correctement listés.
- La ressource Ingress doit être présente et router les requêtes selon l’hôte demandé.
- `make test` doit confirmer que :
  - les requêtes vers `app1.local` renvoient **app1**
  - les requêtes vers `app2.local` renvoient **app2**
  - les requêtes vers `app3.local` renvoient **app3**

## Structure du projet

```text
p2
├── Vagrantfile
├── Makefile
├── README.md
├── scripts
│   └── install_server.sh
└── confs
    ├── app1
    │   ├── deployment.yaml
    │   └── service.yaml
    ├── app2
    │   ├── deployment.yaml
    │   └── service.yaml
    ├── app3
    │   ├── deployment.yaml
    │   └── service.yaml
    └── ingress
        └── ingress.yaml
```

```text
                         +----------------------+
                         |   192.168.56.110     |
                         |   K3s à nœud unique  |
                         +----------+-----------+
                                    |
                                    v
                            +---------------+
                            |    Ingress    |
                            +-------+-------+
                                    |
        +---------------------------+---------------------------+
        |                           |                           |
        v                           v                           v
+---------------+           +---------------+           +---------------+
| Hôte :        |           | Hôte :        |           | Hôte par      |
| app1.local    |           | app2.local    |           | défaut        |
|               |           |               |           | app3.local    |
+-------+-------+           +-------+-------+           +-------+-------+
        |                           |                           |
        v                           v                           v
+---------------+           +---------------+           +---------------+
| Service app1  |           | Service app2  |           | Service app3  |
+-------+-------+           +-------+-------+           +-------+-------+
        |                           |                           |
        v                           v                           v
+---------------+           +---------------+           +---------------+
| Pod app1      |           | Pod app2-x3   |           | Pod app3      |
| 1 réplica     |           | 3 réplicas    |           | 1 réplica     |
+---------------+           +---------------+           +---------------+
```

## Ressources

### Image

![K3s Service/Ingress](https://lh5.googleusercontent.com/ZKJZ5_b2BuaU9KnDYkeCOc0ePvpHQdhfMwvZPjU3pTK5aHL1022YNwY0G9qr4udwae7yI9hgGmhK3g0fuC66HK8ol3BtdVQ_z27nJFfHAfW38oMmhL9Ot8nc3r2Jxel5yTM9h5az33ws0DLwoCKmIZs)

### Articles

* [Pods Kubernetes : créer, observer et comprendre leur cycle de vie](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/pods/)
* [Deployments Kubernetes : déployer et mettre à jour vos applications](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/deployments/)
* [Services Kubernetes : exposer et connecter vos applications](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/services/)
* [Manifests Kubernetes : écrire, valider et corriger rapidement vos manifests](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/ecrire-manifests/)
* [Comment se connecter aux Pods Kubernetes](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/outils/kubectl-exec-debug/)
* [Ingress Kubernetes : exposer vos applications HTTP/HTTPS](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/ingress/)

### Vidéos

* [Kubernetes 005 - Qu’est-ce qu’un Pod ?](https://www.youtube.com/watch?v=maD16sgsFTY&list=PLn6POgpklwWo6wiy2G3SjBubF6zXjksap&index=7)
* [Kubernetes 016 - Qu’est-ce qu’un Deployment ?](https://www.youtube.com/watch?v=AFEU_mBbzr0&list=PLn6POgpklwWo6wiy2G3SjBubF6zXjksap&index=18)
* [Kubernetes 018 - Qu’est-ce qu’un Service ? (objectifs, ClusterIP, exposition...)](https://www.youtube.com/watch?v=Z62WCbIIWyg&list=PLn6POgpklwWo6wiy2G3SjBubF6zXjksap&index=20)

## Utilisation de l’IA

J’ai principalement utilisé l’IA pour m’aider à comprendre certains concepts, générer des schémas et créer des quiz.

## Auteur

* **Login :** jmougel
* **GitHub :** [jasonmgl](https://github.com/jasonmgl)

[Retour](../README.md)