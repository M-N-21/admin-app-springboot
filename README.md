# Projet admin-app

Ce projet est un projet Spring Boot. Il utilise Java 8, maven, docker, logs,...

## Structure du Projet

Le projet est organisé en plusieurs packages pour une meilleure séparation des préoccupations :

### `sn.isi.entities`

Ce package contient les entités JPA avec les annotations nécessaires pour la génération de la base de données.

### `sn.isi.dto`

Ce package contient des classes DTO (Data Transfer Object) pour les mêmes entités sans les annotations. Cela permet d'éviter de manipuler directement les objets de la base de données dans les vues.

### `sn.isi.dao`

Ce package contient des interfaces DAO (Data Access Object) pour chaque entité qui étendent `JpaRepository` pour la gestion des opérations CRUD.

### `sn.isi.mapping`

Ce package contient des interfaces pour les mappers qui permettent de transformer chaque entité en sa correspondance DTO et vice versa.

### `sn.isi.exception`

Ce package contient des classes personnalisées pour la gestion des exceptions.

### `sn.isi.service`

Ce package contient des services pour chaque entité qui gèrent les opérations CRUD et d'autres plus personnalisés associées.

### `sn.isi.config`

Ce package contient des configurations pour l'application, y compris la gestion des messages sources ainsi que la configuration des logs

### `sn.isi.controller`

Ce package contient des RestControllers pour chaque entité pour gérer les opérations REST.

### `sn.isi`

Ce package contient la classe de base pour le démarrage de l'application Spring Boot.

## Annotations Utilisées

- Les entités JPA dans le package `sn.isi.entities` sont annotées avec `@Entity` pour indiquer qu'elles sont persistantes.
- Les interfaces DAO dans le package `sn.isi.dao` étendent `JpaRepository` et sont annotées avec `@Repository`.
- Les classes services dans le package `sn.isi.service` sont annotées avec `@Service`.
- Les RestControllers dans le package `sn.isi.controller` sont annotés avec `@RestController`.
- Les classes de configuration dans le package `sn.isi.config` sont annotées avec `@Configuration`.
- Les classes de gestion des exceptions dans le package `sn.isi.exception` peuvent utiliser des annotations telles que `@ControllerAdvice` pour gérer les exceptions globalement.

Au niveau du projet dans `sn.isi.resources`, un dossier assez utile où on va mettre nos vues mais aussi c'est à ce niveau que nous avons fait la définissions des messages d'erreur dans `messages.properties` ainsi que la confuguration de base de `log4j` dans `log4j2.xml`. Sans oublier l'un des fichiers fichiers les plus importants `application.yml` ou nous avons défini le port de démarrage de l'application, le datasource pour la base de données, l'orm JPA à travers `hibernate`

## Docker

Vu que nous avons utilisé du docker pour la conteneurisation voici une petite explication de notre fichier `docker-compose.yml` qui se trouve à la racine du projet
Ce fichier est un fichier de configuration YAML utilisé pour définir des services Docker dans le contexte de Docker Compose. Docker Compose est un outil qui permet de définir et de gérer des applications Docker multi-conteneurs. Il permet de spécifier l'ensemble des services, des réseaux et des volumes nécessaires pour qu'une application fonctionne. Ci après le contenu de ce fichier et les explications de chaque ligne.

```
services: # C'est la section principale où vous définissez les services Docker. Chaque service représente un conteneur.

  mysql-admin-db: # Un service qui utilise l'image MySQL version 8.0. Il crée un conteneur nommé container_mysql-admin-db.
    image: mysql:8.0
    container_name: container_mysql-admin-db
    environment: # Les variables d'environnement spécifiées sont utilisées pour configurer le conteneur MySQL, y compris le mot de passe root, la base de données, et les informations d'identification d'utilisateur.
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: adminapp-db
      MYSQL_USER: user
      MYSQL_PASSWORD: user
    ports: # Mappe le port 3306 du conteneur MySQL sur le port 3306 de l'hôte.
      - 3306:3306
    volumes: # Montre un volume Docker nommé mysql_data sur le répertoire /var/lib/mysql à l'intérieur du conteneur. Cela est utilisé pour persister les données de la base de données au-delà de la durée de vie du conteneur.
      - mysql_data:/var/lib/mysql
    healthcheck: # Définit une vérification de santé pour le conteneur en exécutant la commande mysqladmin ping.
      test: mysqladmin ping -h 127.0.0.1 -u $$MYSQL_USER --password=$$MYSQL_PASSWORD

  phpmyadmin-admin-db: # Un service qui utilise l'image PHPMyAdmin la plus récente. Il crée un conteneur nommé container_phpmyadmin-admindb.
    container_name: container_phpmyadmin-admindb
    image: phpmyadmin/phpmyadmin:latest
    ports: # Mappe le port 8085 de l'hôte sur le port 80 du conteneur PHPMyAdmin.
      - 8085:80
    environment: # Configure PHPMyAdmin avec les informations nécessaires pour se connecter au serveur MySQL (PMA_HOST, PMA_USER, PMA_PASSWORD).
      MYSQL_ROOT_PASSWORD: root
      PMA_HOST: mysql-admin-db
      PMA_USER: user
      PMA_PASSWORD: user
    depends_on: # Indique que ce service dépend du service mysql-admin-db et ne devrait démarrer que lorsque mysql-admin-db est prêt.
      - mysql-admin-db
    restart: unless-stopped # Spécifie que le conteneur doit être redémarré sauf s'il a été explicitement arrêté.

volumes: # Cette section définit un volume nommé mysql_data avec un driver local. Ce volume est utilisé pour stocker les données persistantes du conteneur MySQL.
  mysql_data:
    driver: local
```

### Bien à vous `M.N.21`

