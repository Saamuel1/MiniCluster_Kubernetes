# MiniCluster Kubernetes — Simulation IA–HPC
**Auteur : Samuel Duarte Dos Santos**

---

## Objectif du projet

Ce projet a pour but de construire un **mini-cluster Kubernetes local**, configuré entièrement à la main, afin de simuler un environnement HPC (High Performance Computing) sur un poste personnel.  
L’objectif principal est de démontrer la compréhension et la maîtrise des concepts fondamentaux de l’orchestration de conteneurs, du réseau distribué et du stockage partagé, dans une logique de mise en place d’un environnement d’exécution pour des workloads IA.

---

## Architecture du cluster

L’infrastructure est composée de trois machines virtuelles Ubuntu 22.04 :

- **1 master node** : gère le plan de contrôle et l’orchestration du cluster.  
- **2 worker nodes** : exécutent les pods et workloads applicatifs.  
- **1 volume partagé NFS** : simule un système de fichiers distribué de type HPC (analogue à Lustre).

Chaque machine virtuelle dispose de 2 vCPU, 2 Go de RAM et 10 Go d’espace disque.  
Elles sont reliées sur un réseau interne commun via VirtualBox ou Multipass.

L’ensemble repose sur les composants suivants :
- **Kubernetes v1.30.14** installé manuellement via `kubeadm`
- **Containerd** comme runtime de conteneurs
- **Flannel** comme plugin réseau (CNI)
- **NFS** comme solution de stockage partagé

---

## Processus de mise en place

Le déploiement a été réalisé entièrement manuellement, étape par étape :

1. **Création des trois machines virtuelles** : un master et deux workers, tous basés sur Ubuntu 22.04.
2. **Installation des composants Kubernetes** : kubeadm, kubelet, kubectl, containerd et configuration système (désactivation du swap, activation du forwarding IP et des modules noyau).
3. **Initialisation du cluster sur le master node** à l’aide de `kubeadm init` avec une plage réseau adaptée pour Flannel.
4. **Déploiement du réseau de pods** en appliquant la configuration officielle de Flannel.
5. **Jointure des deux workers** au cluster grâce à la commande `kubeadm join` générée automatiquement.
6. **Vérification du cluster** avec `kubectl get nodes`, confirmant la présence et l’état des trois nœuds.
7. **Mise en place d’un stockage partagé NFS**, monté sur tous les nœuds, pour simuler le fonctionnement d’un système de fichiers distribué utilisé dans les environnements HPC.
8. **Déploiement d’un pod de test Nginx** pour valider le bon fonctionnement du réseau et la répartition des charges entre les workers.
9. **Vérification du fonctionnement** : état “Ready” des nœuds, pod en exécution, volume partagé fonctionnel sur l’ensemble du cluster.

---

## Résultats obtenus

À la fin du déploiement :

- Le cluster Kubernetes est **entièrement fonctionnel**, avec un master et deux workers en état `Ready`.
- Le **réseau Flannel** permet la communication inter-pods sur l’ensemble du cluster.
- Le **stockage partagé NFS** est accessible depuis les trois machines, validant la simulation d’un environnement HPC distribué.
- Un **pod Nginx** déployé et accessible confirme le bon fonctionnement de l’orchestration.

Ces résultats démontrent la capacité à configurer et faire fonctionner un environnement Kubernetes sans outils simplifiés (pas de Minikube, pas de k3s), en comprenant chaque composant système impliqué.

---

## Aspects techniques maîtrisés

- Installation manuelle complète de Kubernetes via `kubeadm`
- Configuration du runtime **containerd** avec `SystemdCgroup`
- Gestion des modules noyau (`br_netfilter`, `overlay`) et paramètres `sysctl`
- Déploiement d’un **CNI Flannel** pour la connectivité des pods
- Mise en place d’un **stockage distribué NFS** simulant un système de fichiers HPC
- Diagnostic réseau et résolution d’erreurs (`NotReady`, swap, IP forwarding)
- Structure et vérification multi-nœuds à l’aide de `kubectl`
- Conception d’un environnement reproductible et extensible pour futurs tests IA–HPC

---

## Enseignements clés

Ce projet m’a permis de renforcer plusieurs compétences essentielles :

- **Approche bas-niveau de Kubernetes** : comprendre et manipuler kubeadm, kubelet et containerd sans outils d’abstraction.  
- **Infrastructure distribuée** : interconnexion réseau, communication inter-pods, gestion du control-plane.  
- **Gestion du stockage HPC-like** : configuration et test d’un partage de fichiers entre nœuds.  
- **Résolution de problèmes système** : débogage du kubelet, configuration des cgroups et du kernel, suivi des logs et vérification des états.  
- **Préparation à l’intégration IA–HPC** : base technique pour relier Slurm, Pyxis ou Podman afin d’exécuter des workloads IA dans des conteneurs sur ce cluster.

---

## Perspectives d’évolution

Ce mini-cluster servira de socle à plusieurs extensions :

- Intégration de **Slurm** avec **Pyxis** ou **Slinky** pour exécuter des jobs IA via des conteneurs Podman.  
- Ajout d’un **GPU runtime** (NVIDIA Container Toolkit) pour tester des workloads IA distribués.  
- Mise en place d’une **surveillance Prometheus + Grafana** pour visualiser l’utilisation CPU/GPU/mémoire.  
- Automatisation complète du déploiement avec **Ansible** ou **Terraform**.  
- Exploration de l’orchestration multi-nœuds via Kubernetes pour des **expériences IA parallélisées**.

---

