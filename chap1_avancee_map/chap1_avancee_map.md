# Mise en place 

Créez une application avec Encore

## CLI Symfony

Installez la CLI Symfony

```bash
curl -sS https://get.symfony.com/cli/installer | bash
```

### 01 Exercice

1. Mettez en place la possiblité d'utiliser la CLI sur votre machine.

2. Installez le projet minimaliste.

```bash
symfony new bar 
```

3. Créez la branche start_bar

```bash
git init
git add .
git commit -m "first commit"

git branch start_bar
git checkout start_bar
# git checkout -b start_bar
```

Lancez le serveur sans le protocole HTTPS

```bash
symfony serve --no-tls
```


Installez la gestion des routes 

```bash
composer require doctrine/annotations
```

Installez le moteur de templating 

```bash
composer require twig
```

Pour désinstaller **proprement** une dépendance dans Symfony utilisez la commande suivante

```bash
composer remove twig
```

Installez maintenant le debug de Symfony, en mode development cette dépendance ne sera pas déployer en production.

```bash
composer require debug --dev
```

Installez la possibilité d'injecter des entités en paramètre des méthodes des contrôleurs.

```bash
composer require sensio/framework-extra-bundle
```

Vous pourrez injecter directement le type de l'entité en paramètre des méthodes, SF hydratera l'entité avec le bon identifiant pour récupérer la ressource.

```php
// Une méthode dans un contrôleur 
/**
 * @Route("/beer/{id}", name="show_beer")
 */
public function showBeer(Beer $beer)
{
    // Une instance de l'entité Beer avec l'id récupéré dans l'URL
    dump($beer);
    
    dump($beer->getId()); // affiche l'identifiant passé en paramètre
}

```

### Installez Encore pour la gestion des assets

Encore c'est la couche webpack de SF avec du Node.js et donc JS permettant de gérer les assets (images, CSS, SVG, CSS, JS ...), avec Encore vous allez pouvoir faire également du React ou Vue.js ou Angular. En générale on fait des parties de l'application en React et le reste en Full SF en PHP.

```bash
composer require symfony/webpack-encore-bundle

npm install
```

Considérez maintenant le fichier **package.json**, ce sont les commandes configurer par Encore pour builder les assets pour votre application.

```json
"scripts": {
    "dev-server": "encore dev-server",
    "dev": "encore dev",
    "watch": "encore dev --watch",
    "build": "encore production --progress"
}
```

Dans la console on lancera la commande suivante pour exécuter les commandes ci-dessus.

```bash
npm run watch 
# encore dev --watch sera alors exécutée
```

Installez la gestion des SCSS ajoutez la dépendance suivante ( SASS vous permettent de faire des scripts pour créer les CSS )

```bash
npm install sass-loader@^10.0.0 sass --save-dev
```

Nous allons installer le bootstrap CSS.

```bash
npm install bootstrap --save-dev
```

Pour faire le lien entre les fichiers assets et Twig décommenter dans base.html.twig les lignes suivantes :

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8">
        <title>{% block title %}Welcome!{% endblock %}</title>
        {% block stylesheets %}
            {{ encore_entry_link_tags('app') }}   {# DECOMMENTER # }
        {% endblock %}

        {% block javascripts %}
            {{ encore_entry_script_tags('app') }}  {# DECOMMENTER # }
        {% endblock %}
    </head>
    <body>
        {% block body %}{% endblock %}
    </body>
</html>
```

Modifiez le fichier webpack.config.js, décommentez la ligne suivante 

```js
 .addStyleEntry()
```

## React & Symfony

Installez également React, nous utiliserons plus tard

```bash
npm install react react-dom prop-types --save
```

## Lancez le serveur

Vous avez d'un côté le serveur PHP qui est exécuté par la couche Symfony, nous allons maintenant lancer le serveur Node.js en s'appuyant sur la configuration webpack.config.js

```bash
npm run dev-server -- --port 9000
```

## Contrôleur

Vous allez maintenant installer les maker Symfony pour créer par exemple plus facilement les contrôleurs.

```bash
symfony composer req maker --dev

# Liste les makers
symfony console list make
```

Notez que c'est une dépendance Dev.

Puis créez le contrôleur suivant

```bash
symfony console make:controller BeerController
```


## 02 Exercice Installez les imges dans le dossier assets

Récupérez une image de bière libre de droit, placez la dans le dossier assets de SF

```text
assets/
    images/
        beer_00.jpg
```

Dans le fichier webpack ajoutez la ligne de code suivante, elle précise le dossier d'origine et de destination. Notez que le fichier possède également un hash pour plus de sécurité (écrasement de fichier).

```js
.copyFiles({
    from : './assets/images',
    to : 'images/[path][name].[hash:8].[ext]'
})
```

N'oubliez pas de décommenter la ligne suivante pour mettre à jour dans le fichier build les anciennes images

```js
.cleanupOutputBeforeBuild()
```

Remarque sur la configuration

Lorsque vous installez Encore Symfony crée un fichier de configuration propre aux assets, les images sont "builder" dans le dossier public.

```yaml
framework:
    assets:
        json_manifest_path: '%kernel.project_dir%/public/build/manifest.json'
```

Relancez la commande suivante :

```bash
npm run watch
```
Webpack crée alors un dossier images dans le build du dossier public.

Ouvrez le fichier manifest.json, vous trouverez maintenant la ligne suivante créée par Encore de Symfony :

```json
  "build/images/beer_00.jpg": "/build/images/beer_00.1caf180a.jpg"
```

Webpack a déplacé cette image dans ce dossier. 

Pour le lien vers les images on fera comme suit :

```html
<img src="{{ asset('build/images/beer_00.jpeg') }}" />
```

## Mettez en place la base de données et les entités

```bash
# ORM Doctrine
composer require symfony/orm-pack
# Makers de Doctrine
composer require --dev symfony/maker-bundle
```
