---
title: Procédure sur l'accès à un sevrice http sur la machine de virtuelle
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

# Accès à un service **HTTP** sur la **VM**
## Installation d'un premier service pour tester
Installez le serveur HTTP `nginx` sur votre machine virtuelle.

```bash
user@matrix sudo apt-get install nginx
```

Vérifiez que le serveur est démarré avec la commande `systemctl`.

```bash
user@matrix systemctl status nginx.service
```

Installez le client HTTP en mode texte `curl`.

```bash
user@matrix sudo apt-get install curl
```

Vérifiez si vous pouvez accéder au serveur `nginx` depuis la VM avec la commande.

```bash
user@matrix$ curl http://localhost
```

## Accès au service depuis la machine physique
Nous souhaitons maintenant accéder au service s'exécutant sur la *machine virtuelle* depuis la machine physique.

Lorsqu'on exécute un service sur notre *machine virtuelle*, ce service est généralement accessible uniquement à l'intérieur de la *machine virtuelle* elle-même, car elle fonctionne dans son propre environnement isolé.

Lorsqu'on souhaite accéder à ce service depuis la *machine physique*, cela devient un peu plus complexe. En général, les *machines virtuelles* sont isolées du **réseau extérieur pour des raisons de sécurité**. C'est ici qu'intervient la notion de **tunnel SSH**.

Pour résoudre ce problème, nous allons utiliser la fonction **tunnel** de SSH.

Pour cela, suivez ces étapes :

```bash
login@phys$ ssh -L 0.0.0.0:9090:localhost:80 vmjump
```
Cette commande crée un tunnel SSH, permettant à la machine physique d'accéder au service HTTP de la machine virtuelle via le port `9090`.