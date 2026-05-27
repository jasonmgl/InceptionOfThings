# Partie 1 - Construire un cluster K3s à 2 nœuds avec Vagrant

*Ce projet a été réalisé dans le cadre du cursus 42 par jmougel, klombard et mmorot.*

[Retour](../README.md)

## Description

La partie 1 de ce projet consiste à créer et approvisionner un cluster K3s à 2 nœuds à l’aide de Vagrant.

Deux machines virtuelles sont déployées automatiquement :
* la première exécute **K3s** en **mode serveur**
* la seconde rejoint le cluster en **mode agent**

L’objectif est de construire un environnement Kubernetes minimal avec un provisionnement automatisé et un accès SSH sans mot de passe entre les machines.

### Contraintes

* Utiliser la dernière version stable de la distribution Linux de votre choix comme système d’exploitation.
* Les machines doivent être exécutées avec Vagrant.
* Les noms des machines doivent correspondre au login d’un membre de votre équipe.
* Le hostname de la première machine doit se terminer par la lettre majuscule **S** (pour **Server**).
* Le hostname de la seconde machine doit se terminer par **SW** (pour **ServerWorker**).
* L’adresse IP de la première machine (**Server**) doit être **192.168.56.110**.
* L’adresse IP de la seconde machine (**ServerWorker**) doit être **192.168.56.111**.
* Vous devez pouvoir vous connecter aux deux machines en SSH sans mot de passe.
* **K3s** doit être installé sur la première machine (**Server**) en mode serveur.
* **K3s** doit être installé sur la seconde machine (**ServerWorker**) en mode agent.

## Stack technique

* **Langages :** Bash, YAML
* **Outils :** Vagrant, K3s

## Instructions

### Installation

```bash
git clone https://github.com/jasonmgl/InceptionOfThings
cd InceptionOfThings/p1
make up
```

Assurez-vous que les outils suivants sont installés sur votre système :

* Make
* Vagrant
* VirtualBox

### Utilisation

Un **Makefile** est fourni pour faciliter l’exécution du projet. Les commandes suivantes sont disponibles :

| Commande | Description |
|---------|-------------|
| `make up` | Démarre le cluster Vagrant. |
| `make down` | Arrête proprement le cluster Vagrant. |
| `make provision` | Réapprovisionne le cluster Vagrant si les scripts ont été mis à jour. |
| `make clean` | Supprime le fichier contenant le token du nœud K3s. |
| `make fclean` | Exécute `clean`, puis supprime les machines virtuelles Vagrant et les fichiers de cache. |
| `make help` | Affiche la liste des commandes disponibles. |

### Validation

Vous pouvez vérifier que l’environnement fonctionne correctement avec :

```bash
vagrant status
vagrant ssh jmougelS
sudo kubectl get nodes -o wide
```

Résultat attendu :

- Les deux machines virtuelles doivent apparaître dans l’état `running` dans `vagrant status`.
- Vous devez pouvoir vous connecter à `jmougelS` en SSH sans demande de mot de passe.
- La commande `kubectl get nodes -o wide` doit afficher les deux nœuds dans l’état `Ready`.
- Le premier nœud doit agir comme serveur K3s.
- Le second nœud doit avoir rejoint le cluster en tant que nœud worker/agent.

## Structure du projet

```text
p1
├── Vagrantfile
├── Makefile
├── README.md
└── scripts
    ├── install_server.sh
    └── install_worker.sh
```

## Ressources

### Images

![Architecture K3s](https://framerusercontent.com/images/dWSIayJXKNQbiDmppiNvPcemA.jpeg)

### Articles

* [Maîtriser Vagrant pour vos environnements](https://blog.stephane-robert.info/docs/infra-as-code/provisionnement/vagrant/)
* [K3s : Kubernetes léger pour l’edge, l’IoT et le homelab](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/k3s/)
* [Deployments Kubernetes : déployer et mettre à jour vos applications](https://blog.stephane-robert.info/docs/conteneurs/orchestrateurs/kubernetes/deployments/)
* [Options de configuration](https://docs.k3s.io/installation/configuration)

### Vidéos

* [Kubernetes 000 - Préambule : pourquoi ?](https://www.youtube.com/watch?v=KViZkMialxo&list=PLn6POgpklwWo6wiy2G3SjBubF6zXjksap&index=1)
* [Kubernetes 001 - Histoire, contexte et solutions](https://www.youtube.com/watch?v=eRH_cetVAck&list=PLn6POgpklwWo6wiy2G3SjBubF6zXjksap&index=2)
* [Kubernetes 002 - Architecture : déclaratif vs impératif](https://www.youtube.com/watch?v=56i8lXmAtUw&list=PLn6POgpklwWo6wiy2G3SjBubF6zXjksap&index=3)
* [Kubernetes 003 - Schéma d’architecture : comment ça fonctionne ?](https://www.youtube.com/watch?v=PlraENp_bMk&list=PLn6POgpklwWo6wiy2G3SjBubF6zXjksap&index=4)
* [Kubernetes 004 - Scheduling : de l’ETCD au conteneur](https://www.youtube.com/watch?v=M7o21hmxI28&list=PLn6POgpklwWo6wiy2G3SjBubF6zXjksap&index=5)

## Utilisation de l’IA

J’ai principalement utilisé l’IA pour m’aider à comprendre certains concepts, générer des schémas et créer des quiz.

## Auteur

* **Login :** jmougel
* **GitHub :** [jasonmgl](https://github.com/jasonmgl)

[Retour](../README.md)