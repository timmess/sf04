# Login

Créez une page de login, il faut dans un premier temps installer le bundle de securité.

```bash
composer require symfony/security-bundle

# Suivez les instructions
php bin/console make:user
```

Créez une fixuture pour les users.

Ajoutez un limiteur pour les attaques bruts forces.

```bash
composer require symfony/rate-limiter
```

Dans le fichier de sécurité ajouter :

```yaml
 # configure the maximum login attempts in a custom period of time
 main:
     # ...
     # configure the maximum login attempts in a custom period of time
     login_throttling:
         max_attempts: 3
         interval: '15 minutes'
```

N'oubliez pas de créer la migration et de créer la table users.

```bash
php bin/console make:fixtures
```

1. Créez un utilisateur ayant le rôle administrateur et le rôle visiteur. L'entité User sera relié à l'entité client dans une relation OneToOne.

2. Créez maintenant un formulaire de connexion et une fois connecté donnez la possiblité de donner un score à une bière.

*Indications : il faudra donner la possiblité de se connecter à l'application à l'aide d'une page de connexion.*

3. Créez un formulaire, on ne pourra laisser un score que si on est connecté. Utilisez également la validation de Symfony pour les données du formulaire, seul le type numérique et le champ non vide sera vérifié pour le formulaire.

Le formulaire affichera uniquement la possibilité de donner un score. Le reste des valeurs de l'entité Statistic sera hydraté logiquement par vos soins.

```bash
composer require symfony/form

# Validation des données du formulaire
composer require symfony/validator
```

Remarque : pour savoir si vous êtes connecté dans Twig utilisé **app.user**