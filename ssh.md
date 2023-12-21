---
title: Procédure sur l'utilisation de la commande ssh
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

# Faciliter la connexion
## Générer une paire de clés
Pour générer une paire de clés, il vous suffit de saisir la commande suivante :
```bash
login@phys$ ssh-keygen
```

## Transmettre la clé publique au serveur
Pour fournir la clé SSH à "dattier.iutinfo.fr", exécutez la commande suivante :     
```bash
login@phys$ ssh-copy-id -i .ssh/id_rsa.pub dattier.iutinfo.fr
```

Ensuite, il vous sera demandé le mot de passe de la session qui contient la clé privée. Ensuite, effectuez une connexion SSH vers "dattier.iutinfo.fr". Si c'est la première fois que vous vous connectez, l'ordinateur demandera le mot de passe de votre fichier contenant la clé privée.

Et voilà, le tour est joué. Vous devriez maintenant pouvoir vous connecter à "dattier.iutinfo.fr" sans avoir à saisir de mot de passe.

# Configuation ssh
## Création d'un alias *vm*

Créez un alias `vm` pour vous connecter directement à votre VM depuis votre machine physique en utilisant la directive `ProxyJump`. En d'autres termes, la commande `ssh vmjump` se connectera d’abord à la machine de virtualisation, puis automatiquement à la VM, sans autre manipulation de votre part.

Pour ce faire, modifiez le fichier `config` dans le dossier `.ssh`. Vous pouvez le faire avec les commandes suivantes :
```bash
login@phys$ nano .ssh/config
```

Ajoutez ensuite les lignes suivantes au fichier `config` :
```bash
Host vmjump
      Hostname 10.42.xx.yy
      ProxyJump virt
      User user
```

Assurez-vous de remplacer les xx et yy par les nombres de l'adresse IP que vous avez attribuée à votre machine `matrix`.

Cela vous permettra de vous connecter directement à votre machine virtuelle en utilisant l'alias `vmjump` sans avoir à passer par la machine de virtualisation manuellement.