---
title: Procédure sur l'installation de Postgres
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

# Installation et configuration basique d'un serveur de base de données
## Installer PostgreSQL
Pour installer PostgreSQL, vous pouvez utiliser le script suivant :
```bash
user@matrix$ sudo sh -c 'echo "deb https://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'

user@matrix$ wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

user@matrix$ sudo apt-get update

user@matrix$ sudo apt-get -y install postgresql
```

Pour vérifier si l'installation s'est bien déroulée et démarrer le service, utilisez la commande :
```bash
user@matrix$ systemctl status postgresql
```
Si tout s'est bien passé, vous devriez voir des lignes similaires à :
```bash
postgresql service loaded active exited PostgreSQL RDBMS
postgresql@15-main service loaded active running PostgreSQL Cluster 5-main
```

## Gestion des utilisateurs PostgreSQL
Pour créer un utilisateur PostgreSQL :
```bash
user@matrix$ su -

root@matrix# su -l postgres

postgres@matrix# createuser -d -P matrix 
```
L'option `-d` permet à l'utilisateur de créer des bases de données, et `-P` demande un mot de passe.

Pour supprimer un utilisateur PostgreSQL :
```bash
postgres@matrix# dropuser "nom d'utilisateur" 
```

## Gestion des bases de données
Pour créer une base de données :
```bash
postgres@matrix# createdb -O matrix matrix
```
L'option `-O` spécifie que l'utilisateur "matrix" est le propriétaire de la base de données.

Pour supprimer une base de données PostgreSQL :
```bash
postgres@matrix# dropdb "nom de la base" 
```

Pour vérifier que la base de données a bien été créée :
```bash
postgres@matrix# psql -l
```
![Liste des bases de données](./img/psql%20-l.png)

## Se connecter à une base de donnée
Pour se connecter à la base de données :
```bash
user@matrix$ psql -U matrix -h localhost matrix
``` 

## Quelque test sur la base de donnée
Pour créer une table :
```sql
CREATE TABLE test(
  prenom varchar(20), 
  nom varchar(30)
);
```

Pour insérer des valeurs dans une table :
```sql
INSERT INTO test VALUES ('arthur', 'keller');
```

Pour voir les valeurs de votre table :
```sql
SELECT * FROM test;
```