---
title: Procédure sur l'installation de nginx sur la machine de virtuelle
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

# Installation de Synapse

Il est temps d'installer Synapse, l'implémentation de référence pour un serveur Matrix.

## Installation du paquet sous Debian

Lors de l'installation, le gestionnaire de paquets vous demandera le nom de votre instance. Vous devez indiquer `machine-physique.iutinfo.fr:8008`. Assurez-vous de bien spécifier le port `8008` et de remplacer `machine-physique` par le nom de votre **machine physique** (par exemple `ayou03`).

Pour télécharger Synapse, copiez-collez ce script :

```bash
user@matrix$ sudo apt install -y lsb-release wget apt-transport-https
user@matrix$ sudo wget -O /usr/share/keyrings/matrix-org-archive-keyring.gpg https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg
user@matrix$ echo "deb [signed-by=/usr/share/keyrings/matrix-org-archive-keyring.gpg] https://packages.matrix.org/debian/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/matrix-org.list
user@matrix$ sudo apt update
user@matrix$ sudo apt install matrix-synapse-py3
```

## Paramétrage de l’accès à distance

Par défaut, Synapse n'écoute que sur l'interface de boucle locale. Pour permettre l'accès depuis l'extérieur de la machine virtuelle, il doit également écouter sur l'interface du réseau `10.42.xx.yy/16`.

Modifiez le fichier `homeserver.yaml` :

```bash
user@matrix$ sudo nano /etc/matrix-synapse/homeserver.yaml
```

Modifiez la ligne suivante :

```bash
bind_addresses: ['::1', '127.0.0.1']
```

À :

```bash
bind_addresses: ['::1', '127.0.0.1', '10.42.xx.yy']
```

N'oubliez pas de remplacer `xx.yy` par la bonne valeur.

Ensuite, redémarrez votre serveur avec la commande :

```bash
user@matrix$ sudo systemctl restart matrix-synapse
```

## Paramétrage spécifique pour une instance dans un réseau privé

Notre installation diffère car le serveur n'est pas accessible depuis internet. En production réelle, il serait accessible. Les paramètres par défaut de Synapse considèrent que le serveur est accessible de l'extérieur et qu'il ne cherche pas à contacter des éléments sur un réseau privé. On va donc appliquer la documentation en affectant la variable de configuration `trusted_key_servers` à `[]`.

Modifiez les lignes du fichier `homeserver.yaml` :

```bash
trusted_key_servers:
  - server_name: "matrix.org"
```

À :

```bash
trusted_key_servers: []
#  - server_name: "matrix.org"
```

Redémarrez votre serveur avec la commande :

```bash
user@matrix$ sudo systemctl restart matrix-synapse
```

## Utilisation d’une base Postgres

Synapse utilise par défaut une base de données au format fichier `sqlite`, idéal pour les tests mais insuffisant en production. Changeons la configuration pour utiliser une base `postgres`.

Modifiez les lignes du fichier `homeserver.yaml` :

```bash
database:
  name: sqlite3
  args:
    database: DATADIR/homeserver.db
```

À :

```bash
database:
  name: psycopg2
  args:
    user: <Nom d'utilisateur de votre base de données>
    password: <Mot de passe de votre utilisateur>
    database: <Nom de votre base de données>
    host: localhost
    cp_min: 5
    cp_max: 10
```

Attention : lors de la séance précédente, une base de données matrix a été créée avec des options par défaut incompatibles avec Synapse. Changez la base de données avec ces commandes :

```bash
user@matrix$ sudo su -l postgres

postgres@matrix dropdb <Nom de votre base de données>
postgres@matrix: createdb --encoding=UTF8 --locale=C --template=template0 --owner=matrix <Nom de votre base de données>
```

Redémarrez ensuite votre serveur :

```bash
user@matrix$ sudo systemctl restart matrix-synapse
```

## Création d’utilisateurs

Pour créer un utilisateur, utilisez le script `register_new_matrix_user` installé avec Synapse. Avant cela, ajoutez une ligne au fichier `homeserver.yaml` :

```bash
registration_shared_secret: <Nom de la clé>
```

Redémarrez votre serveur avec la commande :

```bash
user@matrix$ sudo systemctl restart matrix-synapse
```

Maintenant, créez un utilisateur avec la commande suivante puis suivez les instructions :

```bash
user@matrix$ sudo register_new_matrix_user -k <La clé que vous avez mise dans votre fichier de configuration>
```

## Connexion à votre serveur Matrix

Pour vous connecter à votre serveur, un client Element web a été déployé pour vous à l’adresse [http://tp.iutinfo.fr:8888/](http://tp.iutinfo.fr:8888/) mais avant cela, il va falloir quelque petite modification de votre commande ssh. Pour ce faire, taper ces commandes : 
```bash
user@phys$ nano .shh/config
```

Normalement celui ci devrait etre comme ceci :
```bash
Host virt
	Hostname dattier.iutinfo.fr
	ForwardAgent yes

Host vmjump
	Hostname 10.42.111.1
	Proxyjump virt
	User user
	LocalForward 0.0.0.0:9090 localhost:80
```
Si ce n'es pas, je vous invite à retrouner sur [la procédure suivante](./ssh.md). 
Ensuite veuillez rajouter cette ligne pour le `Host vmjump`
```bash
LocalForward 0.0.0.0:8008 localhost:8008
```
Une fois cette ligne rajouter, vous êtes prets à utiliser votre serveur correctement à partir de votre machine physique

Utilisez ce client dans un navigateur de la machine physique et connectez-vous à votre serveur. Attention, par défaut, le client veut se connecter au

 serveur `matrix.org`.

Ajoutez des captures d'écran pour illustrer ces exemples.

Cette étape doit être effectuée pour les deux utilisateurs (sur deux machines physiques différentes).

Créez un salon avec un utilisateur et invitez l’autre utilisateur dans le salon.

## Activation de l’enregistrement des utilisateurs

Pour permettre l'enregistrement de nouveaux utilisateurs, modifiez le fichier `homeserver.yaml` en ajoutant la ligne suivante :

```bash
enable_registration: true
enable_registration_without_verification: true
```

## Fichier `homeserver.yaml`
À la fin de cette procédure, votre fichier devrait présenter la configuration suivante :
```bash
# Configuration file for Synapse.
#
# This is a YAML file: see [1] for a quick introduction. Note in particular
# that *indentation is important*: all the elements of a list or dictionary
# should have the same indentation.
#
# [1] https://docs.ansible.com/ansible/latest/reference_appendices/YAMLSyntax.html
#
# For more information on how to configure Synapse, including a complete accounting of
# each option, go to docs/usage/configuration/config_documentation.md or
# https://matrix-org.github.io/synapse/latest/usage/configuration/config_documentation.html
#
# This is set in /etc/matrix-synapse/conf.d/server_name.yaml for Debian installations.
# server_name: "SERVERNAME"
pid_file: "/var/run/matrix-synapse.pid"
listeners:
  - port: 8008
    tls: false
    type: http
    x_forwarded: true
    bind_addresses: ['::1', '127.0.0.1', '10.42.xx.yy']
    resources:
      - names: [client, federation]
        compress: false
database:
  name: psycopg2
  args:
    user: 'Utilisateur de votre base de données'
    password: 'Le mot de passe de votre utilisateur'
    database: 'Le nom de la base de donnée'
    host: localhost
    cp_min: 5
    cp_max: 10
log_config: "/etc/matrix-synapse/log.yaml"
media_store_path: /var/lib/matrix-synapse/media
signing_key_path: "/etc/matrix-synapse/homeserver.signing.key"
trusted_key_servers: []
#  - server_name: "matrix.org"
registration_shared_secret: 'Nom de votre clé'
enable_registration: true
enable_registration_without_verification: true
```