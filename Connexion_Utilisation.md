---
title: Procédure sur la connexion de la machine de Virtualisation
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

# Connexion à la machine de vitualisation

## Première connexion à la machine de virtualisation
Pour vous connecter à la machine de virtualisation, il vous suffit de saisir la commande suivante :
```bash
login@phys$ ssh dattier.iutinfo.fr
```
Pour fermer la connexion, utilisez l'une des commandes suivantes :
```bash
login@virtu$ exit
ou
login@virtu$ \d
```

## Faciliter la connexion
### Générer une paire de clés
Pour générer une paire de clés, il vous suffit de saisir la commande suivante :
```bash
login@phys$ ssh-keygen
```

### Transmettre la clé publique au serveur
Pour fournir la clé SSH à "dattier.iutinfo.fr", exécutez la commande suivante :     
```bash
login@phys$ ssh-copy-id -i .ssh/id_rsa.pub dattier.iutinfo.fr
```

Ensuite, il vous sera demandé le mot de passe de la session qui contient la clé privée. Ensuite, effectuez une connexion SSH vers "dattier.iutinfo.fr". Si c'est la première fois que vous vous connectez, l'ordinateur demandera le mot de passe de votre fichier contenant la clé privée.

Et voilà, le tour est joué. Vous devriez maintenant pouvoir vous connecter à "dattier.iutinfo.fr" sans avoir à saisir de mot de passe.

# Utilisation de la machine de virtualistion

## Créer et gérer les machines virtuelles
Sur la machine de virtualisation, utilisez les commandes suivantes pour afficher un message d'aide :
```bash
login@virtu$ vmiut
```

Pour créer une machine :
```bash
login@virtu$ vmiut creer "nom de la machine"
```

Pour démarrer la machine :
```bash
login@virtu$ vmiut demarer "nom de la machine"
```

Pour arrêter la machine :
```bash
login@virtu$ vmiut arreter "nom de la machine"
```

Pour lister les machines :
```bash
login@virtu$ vmiut lister
```

Pour obtenir des informations sur la machine :
```bash
vmiut info "nom de la machine"
```

Pour supprimer la machine :
```bash
login@virtu$ vmiut supprimer "nom de la machine"
```

# Connexion à une machine virtuelle
## Console virtuelle
Pour utiliser la console virtuelle, lancez la console en utilisant la commande :
```bash
login@phys$ ssh -x virtu 
```
Ensuite, démarrez la machine virtuelle "matrix" avec la commande :
```bash
login@virtu$ vmiut demarer matrix
```
Cela vous permettra d'accéder à la console virtuelle de la machine virtuelle "matrix".

## Connexion ssh
```bash
login@virtu$ ssh user@10.42.xx.yy
```
Remplacer les xx et yy par les nombre que vous avez en utilisant la commande qui permet de fournir les informations de matrix