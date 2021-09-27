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

Si vous n'avez pas de CLI 

```bash
php bin/console server:start
```

Installez la gestion des routes 

```bash
composer require doctrine/annotations
```

Installez le moteur de templating 

```bash
composer require twig
```

Installez maintenant le debug de Symfony

```bash
composer require debug --dev
```

Installez la possibilité d'injecter des entités en paramètre des méthodes des contrôleurs.

```bash
composer require sensio/framework-extra-bundle
```

### Installez Encore pour la gestion des assets

```bash
composer require symfony/webpack-encore-bundle

npm install
```

Considérez maintenant le fichier package.json

```json
"scripts": {
    "dev-server": "encore dev-server",
    "dev": "encore dev",
    "watch": "encore dev --watch",
    "build": "encore production --progress"
}
```

Installez la gestion des SCSS ajoutez la dépendance suivante

```bash
npm install sass-loader@^10.0.0 sass --save-dev
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

## Bootstrap CSS

Installez le bootstrap et les dépendances.

```bash
npm install bootstrap --save-dev
```

## Installez les imges dans le dossier assets

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
composer require symfony/orm-pack
composer require --dev symfony/maker-bundle
```
