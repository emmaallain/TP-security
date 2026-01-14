# PHP DevOps TP

Petit projet PHP d'exemple pour la classe DevSecOps — génère une image PNG dynamique et sert de démonstrateur pour le packaging Docker, tests et outils de qualité.

## Description

L'application crée une image PNG contenant deux zones colorées et du texte (incluant la date/heure). Le point d'entrée HTTP est le fichier public/index.php qui instancie `App\\ImageCreator`.

## Fonctionnalités

- Génération d'image PNG dynamique (GD + TrueType)
- Chargement des variables d'environnement via `vlucas/phpdotenv`
- Conteneur Docker prêt à l'emploi (Docker/Dockerfile)

## Prérequis

- PHP >= 8.2
- Extension GD
- Composer

## Installation (locale)

1. Installer les dépendances :

```bash
composer install
```

2. Copier `.env.example` si besoin et configurer les variables d'environnement :

```bash
cp .env.example .env
# puis adapter .env
```

3. Lancer un serveur PHP intégré pour tests rapides :

```bash
php -S localhost:8000 -t public
# puis ouvrir http://localhost:8000
```

## Utilisation Docker

Construire l'image :

```bash
docker build -t php-devops-tp -f Docker/Dockerfile .
```

Lancer le conteneur :

```bash
docker run -p 8080:80 php-devops-tp
# puis ouvrir http://localhost:8080
```

## Tests

Exécuter les tests PHPUnit :

```bash
composer install --dev
vendor/bin/phpunit
```

> Remarque : les tests fournis sont des exemples triviales. Il est recommandé d'ajouter des tests unitaires pour `src/ImageCreator.php`.

## Observations et recommandations de sécurité

- `src/ImageCreator.php` concatène `APP_SECRET` directement dans le texte affiché sur l'image : NE PAS AFFICHER DE SECRETS. Retirer cette inclusion ou s'assurer que la valeur imprimée n'est pas sensible.
- `phpunit.xml` référence actuellement un répertoire `<source>` `./app` — le code source se trouve dans `src/`. Corriger ce chemin pour des rapports de couverture valides.
- Ajouter `squizlabs/php_codesniffer` et `phpcompatibility/php-compatibility` en `require-dev` si vous souhaitez utiliser `phpcs.xml` tel quel.

## Fichiers importants

- Point d'entrée HTTP : [public/index.php](public/index.php)
- Génération d'image : [src/ImageCreator.php](src/ImageCreator.php)
- Dockerfile : [Docker/Dockerfile](Docker/Dockerfile)
- Configuration tests : [phpunit.xml](phpunit.xml)

## Prochaines étapes suggérées

- Supprimer l'affichage du secret dans `src/ImageCreator.php`.
- Corriger `phpunit.xml` pour inclure `src/`.
- Ajouter des tests ciblés pour `ImageCreator`.
- Ajouter `phpcs` en dev et exécuter la vérification de style en CI.

## Intégration continue (CircleCI)

Qu'est-ce que CircleCI ?

- CircleCI est un service d'intégration continue (CI) qui exécute automatiquement des pipelines (tests, lint, build, déploiement) à chaque commit ou pull request.

Fichier de configuration :

- Le projet contient un pipeline CircleCI dans `.circleci/config.yml`. Ce fichier définit les jobs et étapes exécutés par CircleCI.

Comment l'activer :

1. Créez un compte sur https://circleci.com et connectez votre dépôt Git (GitHub/GitLab/Bitbucket).
2. Activez le projet sur CircleCI; CircleCI lira automatiquement `.circleci/config.yml` et déclenchera des builds.

Étapes typiques à inclure dans la pipeline :

- Installer les dépendances : `composer install`
- Exécuter les tests : `vendor/bin/phpunit`
- Exécuter l'analyse de code (`phpcs`) si souhaité
- Construire l'image Docker et la publier (optionnel)

Conseils pratiques :

- Placez les variables sensibles (clés, tokens) dans les « Project Settings » > « Environment Variables » de CircleCI (ne pas les stocker dans le dépôt).
- Configurez des caches pour Composer afin d'accélérer les builds.
- Commencez par une pipeline simple (`composer install` + `phpunit`) puis ajoutez linting et build Docker.

---

Faites-moi savoir si vous voulez que j'applique automatiquement les corrections proposées (patchs pour `ImageCreator.php` et `phpunit.xml`) ou que je crée des tests unitaires.
