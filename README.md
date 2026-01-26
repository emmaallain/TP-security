# TP Security - CI/CD Pipeline

Application PHP 8.2 avec pipeline CI/CD automatisé déployant sur VPS via Docker.

## 🚀 Quick Start

### Développement local

```bash
# Cloner le repository
git clone https://github.com/emmaallain/TP-security.git
cd TP-security

# Lancer avec Docker Compose
docker-compose up -d

# Accéder à l'application
open http://localhost:8080
```

### Production

L'application est automatiquement déployée sur le VPS après validation des tests et approbation manuelle dans CircleCI.

## 🏗️ Architecture

```
GitHub Push → CircleCI → Tests/Lint/Security → Build Docker Image → GHCR
                                    ↓
                            Approbation Manuelle
                                    ↓
                          VPS Pull Image + Restart
```

## 🛠️ Stack Technique

- **Backend:** PHP 8.2 + Apache
- **Database:** SQLite
- **CI/CD:** CircleCI
- **Containerisation:** Docker + Docker Compose
- **Registry:** GitHub Container Registry (GHCR)
- **Secrets Management:** Doppler
- **Infrastructure:** VPS Ubuntu avec Docker

## 📋 Pipeline CI/CD

Le pipeline CircleCI exécute automatiquement :

1. **Quality Checks**
   - PHP_CodeSniffer (PHPCS) - Analyse statique
   - PHPCompatibility - Compatibilité PHP
   - Local PHP Security Checker - Scan des vulnérabilités
   - PHPUnit - Tests unitaires

2. **Build & Push**
   - Construction de l'image Docker
   - Tag avec le nom de la branche
   - Push sur GHCR (`ghcr.io/emmaallain/tp-security`)

3. **Deployment** (après approbation manuelle)
   - Récupération des secrets via Doppler
   - Connexion SSH au VPS
   - Pull de la dernière image Docker
   - Redémarrage des containers avec Docker Compose

## 🔐 Configuration Secrets

Les secrets sont gérés dans Doppler avec les variables suivantes :

- `VPS_USER` - Utilisateur SSH
- `VPS_IP` - IP du VPS
- `VPS_DEPLOY_DIRECTORY` - Dossier de déploiement
- `GHCR_USERNAME` - Username GitHub
- `GHCR_PAT` - Personal Access Token GitHub

Dans CircleCI, ajouter uniquement :
- `DOPPLER_TOKEN` - Service Token Doppler
- `VPS_SSH_FINGERPRINT` - Fingerprint de la clé SSH

## 📦 Structure du Projet

```
.
├── .circleci/
│   └── config.yml              # Configuration CI/CD
├── Docker/
│   └── Dockerfile              # Image PHP 8.2 Apache
├── docker-compose.yml          # Dev local
├── docker-compose.production.yml  # Production VPS
├── src/                        # Code source PHP
├── public/                     # Point d'entrée web
├── database/                   # Base SQLite
├── phpcs.xml                   # Règles PHPCS
└── phpunit.xml                 # Configuration PHPUnit
```

## 🚢 Déploiement Manuel

En cas de besoin, déployer manuellement sur le VPS :

```bash
# Se connecter au VPS
ssh root@<VPS_IP>

# Naviguer vers le dossier
cd /var/www/tp-security

# Pull et restart
docker-compose pull
docker-compose up -d
```

## 🔄 Rollback

Pour revenir à une version précédente :

```bash
# Sur le VPS
export TAG=<ancien_tag>
docker-compose pull
docker-compose up -d
```

## 📊 Monitoring

Consulter les logs de l'application :

```bash
# Sur le VPS
docker-compose logs -f app
```

## 🧪 Tests

Lancer les tests localement :

```bash
# Avec Docker
docker-compose exec app ./vendor/bin/phpunit

# Ou localement
composer install
./vendor/bin/phpunit
```

## 📝 Documentation

Pour plus de détails sur l'architecture, les problèmes rencontrés et les solutions, consulter [RAPPORT.md](./RAPPORT.md).

## 🤝 Contribution

1. Créer une branche feature : `git checkout -b feature/ma-feature`
2. Commit : `git commit -m 'Add ma-feature'`
3. Push : `git push origin feature/ma-feature`
4. Ouvrir une Pull Request

Les workflows CircleCI se déclencheront automatiquement sur toutes les branches.

## 📄 License

Ce projet est un TP académique.

---

**Note:** Ce projet utilise Doppler pour la gestion des secrets. Une migration depuis Infisical a été effectuée en raison de problèmes d'authentification avec leur API.
