---
title: Procédure sur la configuration de la machine virtuelle
author: Arthur KELLER, Gaspard SOULIEZ
header-includes: 
  - \usepackage{fancyhdr}
  - \pagestyle{fancy}
  - \fancyhead{}
  - \fancyhead[C]{SAE3.03 - Déploiement d'une application}
  - \fancyhead[R]{\thepage}
  - \fancyfoot{}
  - \fancyfoot[C]{\thepage}
  - \renewcommand{\headrulewidth}{0.4pt}
  - \renewcommand{\footrulewidth}{0pt}
---
\newpage

# Configuration du réseau

## Changement de la configuration 
Commencez par désactiver l'interface réseau avec la commande :
```bash
root@matrix# ifdown enp0s3
```

Ensuite, modifiez le fichier interfaces à l'aide de la commande :
```bash
root@matrix# nano /etc/network/interfaces
```
Modifiez les lignes comme suit :
```bash
allow-hotplug enp0s3
iface enp0s3 inet dhcp 
```
par
```bash
allow-hotplug enp0s3
iface enp0s3 inet static
      address 10.42.111.1/16
      gateway 10.42.0.1
```
Une fois les modifications effectuées, redémarrez la machine avec la commande :
\newpage
```bash
root@vm# reboot
```
Ces étapes permettent de couper l'interface réseau, modifier la configuration réseau dans le fichier interfaces, puis redémarrer la machine virtuelle pour appliquer les changements.

# Configurer et mettre à jour la machine virtuelle
## Connexion **root** et **SSH**
Essayez de vous connecter à la machine virtuelle en SSH sur le compte `root`.

1. Quelle commande avez vous utilisée ?
```bash
ssh root@10.42.111.1
```
2. Que se passe-t’il ?

Il nous renvoie `Permission denied, please try again.`

3. Pourquoi ?

Cela se produit car la connexion directe en tant que `root` est souvent désactivée par mesure de sécurité.

Reconnectez vous, cette fois avec le compte user et lisez la page de manuel `su`

1. Quelle est la signification de l’option --login ?
L'option --login permet de démarrer une nouvelle session de connexion, en réinitialisant l'environnement d'environnement.

2. Pourquoi est-il intéressant de l'utiliser ?
Il est intéressant de l'utiliser pour se connecter à un autre compte utilisateur avec un nouvel environnement, ce qui peut être utile pour des raisons de sécurité et de gestion des autorisations.

## Mise à jour
Pour effectuer une mise à jour, il suffit de saisir les commandes suivantes :
```bash
root@matrix# apt update && apt full-upgrade
```
Une fois la mise à jour terminée, comme il est probable qu’une nouvelle version du noyau ait été installée, redémarrez la machine virtuelle :
```bash
root@matrix# reboot
```
Ces commandes assurent que votre système est à jour avec les derniers correctifs de sécurité et les mises à jour logicielles disponibles. N'oubliez pas de redémarrer la machine après une mise à jour qui inclut une nouvelle version du noyau pour que les changements prennent effet.

## Installation des outils
Pour installer les outils suivant : 
* vim
* less
* tree
* rsync

Utiliser la commande suivante :
```bash
root@matrix# apt instal tree vim less rsync
```
Ces commandes utiliseront le gestionnaire de paquets APT pour télécharger et installer les outils spécifiés sur votre système. Une fois l'installation terminée, vous pourrez utiliser ces outils pour diverses tâches, telles que l'édition de texte avec Vim, la visualisation du contenu des fichiers avec Less, la navigation dans la structure du répertoire avec Tree, et la synchronisation de fichiers avec Rsync.

# Dernière configuration de la machine virtuelle
## Changement du nom de machine

Nous allons changer le nom de la machine virtuelle pour qu'elle s'appelle `matrix`, et ce nom devra être conservé au redémarrage de la machine. Pour cela, suivez ces étapes :

1. Modifiez le fichier `/etc/hostname` en utilisant la commande :
```bash
root@vm# nano /etc/hostname
```
et remplacez le nom actuel (`debian`) par `matrix`. Enregistrez les modifications.

2. Redémarrez votre machine avec la commande :
```bash
root@vm# reboot
```
Assurez-vous de redémarrer la machine pour que les modifications prennent effet.

![Fichier hostname](./img/hostname.png){width=200px;}

3. Modifiez également le fichier `/etc/hosts` en utilisant la commande :
```bash
root@vm# nano /etc/hosts
```
et changez le nom de la machine correspondant à l'adresse IP.

![Fichier hosts](./img/Capture%20du%202023-11-10%2011-33-46.png){width=200px;}

Ces étapes garantissent que votre machine virtuelle est désormais appelée `matrix` et que ce nom est préservé après le redémarrage.

## Installation et configuration de **sudo**
Pour installer sudo, utilisez la commande suivante :
```bash
root@matrix# apt-get install sudo
```

Pour permettre à un utilisateur d'utiliser les commandes `sudo`, utilisez l'une des commandes suivantes :
```bash
root@matrix# usermod -aG sudo "nom de l'utilisateur voulu"
```
ou
```bash
root@matrix# adduser "nom utilisateur" sudo
```

Testez si les changements ont bien été pris en compte en effectuant les étapes suivantes :
```bash
user@matrix# su user

user@matrix# sudo date
```

Si le système vous demande votre mot de passe, cela signifie que vous avez les droits sudo.