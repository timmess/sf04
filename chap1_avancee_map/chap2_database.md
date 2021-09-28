# Symfony et Doctrine

Nous allons dans ce chapitre mettre en place la base de données et découvrir Doctrine.

Pour cela nous allons installer le bundle Doctrine, c'est un ORM (object relation model) une couche d'abstraction PHP objet sur les données.

*Ces deux composant ou bundle permettront respectivement d'avoir l'ORM Doctrine et les commandes make associées.*

```bash
composer require symfony/orm-pack
composer require --dev symfony/maker-bundle

```

 Dans le fichier .env - fichier de configuration des constantes de l'application - à la racine du projet renseignez le nom de votre base de données ainsi que les login/password, adaptez le code à vos besoin ci-dessous :

```text
 ###> doctrine/doctrine-bundle ###
# Format described at http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/configuration.html#connecting-using-a-url
# For an SQLite database, use: "sqlite:///%kernel.project_dir%/var/data.db"
# Configure your db driver and server_version in config/packages/doctrine.yaml
DATABASE_URL=mysql://login:password@127.0.0.1:3306/db_simplebar
###< doctrine/doctrine-bundle ###
```

Puis lancez la commande suivante pour créer effectivement la base de données :

```bash
php bin/console doctrine:database:create
```

Voici le diagramme UML des tables qui explicite les relations que nous devons implémenter entre les tables :

![database schema](images/simplebar_01.png)

Une bière aura 0 à plusieurs catégorie(s) et une catégorie comportera 0 à plusieurs bière(s). Chaque bière sera ratachée à un au plus pays.

Doctrine va nous permettre d'implémenter ces relations en base de données.

## Créez l'entité Country

Vous allez commencer par créer la table country à, notez qu'avec Doctrine la convention c'est de créer d'abord l'entité, la classe PHP, elle sera au singulier (convention). Par la suite nous déclencherons une deuxième méthode qui permettra de créer la table en base de données. Tapez la ligne de commande suivante, puis nommez votre entity Country :

```bash
php bin/console make:entity
```

Aidez-vous de la documentation Symfony pour créer cette table, il vous suffit de répondre aux questions dans la console. Lisez bien les remarques dans la console elle-même lors de la création de l'entité.

## Création de l'entité Beer

Tapez la même commande que précédement, créez uniquement les champs : name, description et published_at en respectant les types du diagramme UML ci-dessus. Si vous vous trompez supprimer les fichiers se trouvant dans les dossiers Entity et Repository correspondant à votre entity et recommencez.

Créez la relation entre l'entité Country et Beer, pour se faire ajoutez un champ country à l'entité Beer et indiquez à Doctrine que vous souhaitez faire du **ManyToOne**, en effet plusieurs bières peuvent se trouver dans le même pays.

Une relation **ManyToOne** se traduit par **N..1** c'est-à-dire une relation qui permet à une entité A d'avoir une relation avec plusieurs entités B.

Dans l'entité Beer nous allons relier Beer à Country de la manière suivante, cette entité est appelée entité propriétaire car elle possède la clé secondaire ici **country**. Nous précisons que c'est du **ManyToOne**. En effet, plusieurs bières peuvent se trouver dans au plus un même pays, noté que nous parlerons d'entité inverse pour l'entité Country, n'écrivez pas à la main ce qui suit, vous devez l'implémenter à partir de la console (make:entity) :

```php

// DANS LA CLASSE Beer

/**
* @ORM\ManyToOne(targetEntity="App\Entity\Country",
* inversedBy="beers")
*/
private $country;
```

## Création de l'entité Category

Créez simplement la table Category avec un name uniquement, puis indiquez à Doctrine que vous souhaitez mettre en relation Beer et Category dans une relation ManyToMany, une bière peut se trouver dans une ou plusieurs catégories et réciproquement une catégorie peut contenir plusieurs bières.

*Remarques : si vous vous trompez vous pouvez modifiez l'entité en donnant le nom de l'entité à modifier.*

## Migration

Pour créer le fichier de migration tapez la ligne de commande suivante, il permet de générer un fichier PHP dans lequel toutes les commandes SQL sont écrites afin de créer effectivement les tables en base de données, bien sûr il faudra exécuter une autre commande pour passer à la création des tables en base de données :

```bash
php bin/console make:migration
```

Par la suite vous taperez la ligne suivante afin de créer les tables en base de données :

```bash
php bin/console doctrine:migrations:migrate
```

## Insertion de données dans la table Beer

Nous allons maintenant créer quelques bières et les afficher en page d'accueil, nous allons pour se faire créer une méthode spécifique dans notre contrôleur BarController.

Créez maintenant dans le contrôleur BarController la méthode suivante **createBeer**, nous l'utiliserons pour insérer des données dans la table Beer :

```php

/**
     * @Route("/newbeer", name="create_beer")
     */
    public function createBeer(){
        $entityManager = $this->getDoctrine()->getManager();

        $beer = new Beer();
        $beer->setname('Super Beer');
        $beer->setPublishedAt(new \DateTime());
        $beer->setDescription('Ergonomic and stylish!');

        // tell Doctrine you want to (eventually) save the Product (no queries yet)
        $entityManager->persist($beer);

        // actually executes the queries (i.e. the INSERT query)
        $entityManager->flush();

        return new Response('Saved new beer with id '.$beer->getId());
    }

```

## Fixtures Doctrine, insertion de données dans les tables

Nous allons maintenant utiliser des fixtures pour insérer des données d'exemple dans la ressource Beer en base de données, en ligne de commande tapez :

```bash
composer require --dev doctrine/doctrine-fixtures-bundle
```

Vous devriez maintenant avoir dans le dossier src le dossier **DataFixtures** ainsi qu'une classe **AppFixtures**. C'est cette dernière classe qui va nous permettre d'insérer des données automatiquement en base de données.

Installez également **Faker** pour le développement c'est un component permettant d'insérer des données d'exemple. Il permet d'avoir des patterns d'exemple comme des numéros de téléphone, adresse, nom, texte, ...

Vous trouverez la documentation officiel à l'adresse suivante :
[https://github.com/FakerPHP/Faker](https://github.com/FakerPHP/Faker)

```bash
composer require --dev fakerphp/faker
```

Dans le fichier AppFixtures.php précisez le namespace de la dépendance Faker comme suit (autoloader), précisez également le nom des entités avec lesquelles vous souhaitez travailler :

```php

<?php

namespace App\DataFixtures;

use Faker;
use App\Entity\Beer;

use Doctrine\Bundle\FixturesBundle\Fixture;
use Doctrine\Common\Persistence\ObjectManager;

class AppFixtures extends Fixture
{
    public function load(ObjectManager $manager)
    {
        $faker = Faker\Factory::create('fr_FR');
        // ICI vous devez coder l'insertion des données
        // avec les patterns Faker
    }
}

```

## Exercice Faker et AppFixtures

Insérez des données à l'aide de AppFixture et de Faker, puis tapez la ligne de commande suivante :

```bash
php bin/console doctrine:fixtures:load
```

Pour plus d'information sur ces commandes reportez-vous à la documentation officiel :
[Fixture](https://symfony.com/doc/master/bundles/DoctrineFixturesBundle/index.html)

## Exercice Affichez les bières en page d'accueil

Vous allez maintenant afficher les bières en page d'accueil. Pour cela vous allez utiliser la classe Repository de l'entité Beer.

Dans le contrôleur BarController Symfony met à notre disposition, sous forme d'un service, Doctrine. Nous pouvons donc utiliser le repository Beer et sa méthode findAll comme suit pour récupérer l'ensemble des ressources de cette table :

```php

$repository = $this->getDoctrine()->getRepository(Beer::class);
$beers = $repository->findAll();

```

Affichez maintenant les bières en page d'accueil. Notez que la syntaxe dans le template Twig pour afficher les données est différentes du PHP habituel :

```html

{% for beer in beers %}
    {{ beer.name }}
    {# attention la date est retournée dans un objet
    il faut donc appliquer un pipe | et une fonction
    dessus pour afficher la date correctement :
     #}
    {{ beer.publishedAt | date('d/m/Y')}}
{% endfor %}

```

## Exercice Ajoutez un champ price

Reprenez l'entité Beer et ajouter un price à celle-ci. Pour se faire il suffit de relancer la commande suivante, notez que pour un décimale vous préciserez que ce dernier est sur 5 chiffres significatifs avec 2 chiffres après la virgule :

```bash

# Ajoutez le nouveau champ ...
php bin/console make:entity

# Créez le fichier de migration en tenant compte de
# l'état de la base de données et des entités
php bin/console doctrine:migrations:diff

```

Mettez à jour les fixtures en ajoutant à l'aide de faker des prix à vos bières.

![database schema](images/simplebar_02.png)

## Création des countries

Dans AppFixtures créez des pays, voir la liste ci-dessous, puis en utilisant setCountry de Beer essayez d'associer un pays à une bière :

```php
$countries = ['belgium', 'french', 'English', 'germany'];
```

## Routes beer

Ajoutez un lien dans le code Twig de la page d'accueil pour accéder à la page affichant les bières d'un pays. Vous afficherez ce lien pour chaque bière (si la bière est associé à un pays), voyez ce lien comme un tag ou mot clé de la bière.

Vous devez créer une action (méthode), par exemple show_beer, dans le contrôleur BarController. Attention cette méthode possède un paramètre qui est techniquement l'identifiant unique correspondant au pays.

Pour le lien vous utiliserez le helper de Symfony "path" de la manière suivante :

```html
{#
Twig
Votre url aura la forme suivante : http://localhost:8000/country/1
notez que country_beer est le nom de votre route (voir les annotations)
#}
{% if beer.country %}
    <a href="{{ path('country_beer', { 'id' : beer.country.id }) }}">
        {{ beer.country.name }}
    </a>
{% endif %}
```

## Catégories

Modifiez l'entité Category et ajoutez un champ "term", par défaut ce champ est "normal".

Créez les catégories suivantes, chaque bière aura une catégorie "normal" et au moins une catégorie spéciale :

```php
// catégories normals
$categoriesNormals = ['blonde', 'brune', 'blanche'];

// catégories specials
$categoriesSpecials = ['houblon', 'rose', 'menthe', 'grenadine', 'réglisse', 'marron', 'whisky', 'bio'] ;

```

Remarque lorsque vous allez modifier l'entité et donc par la suite la table SQL pensez à exécuter la commande suivante :

```bash
# Compare l'état de la base de données et créer le fichier de migration
php bin/console doctrine:migration:diff
```

Vous pouvez également lancer une fixture spécifique maintenant (elles sont toutes séparées dans des fichiers)

```bash
# La fixture pour les catégories
php bin/console doctrine:fixtures:load --group=CategoryFixtures
```

Créez ces catégories et associées ces catégories aux bières déjà créés à l'aide de votre AppFixtures. Mettez à jour les données.

Vous allez créer maintenant dans le repository CategoryRepository une méthode findByTerm, elle permettra de récupérer les catégories selon leur terme propre.

Affichez pour l'instant dans un dump dans le constructeur du Controller BarController pour vérifier que vous récupérez bien ces catégories.

```php
class BarController extends AbstractController
{

    public function __construct()
    {
        $repository = $this->getDoctrine()->getRepository(Beer::class);
        dump($repository->findByTerm('special'));
    }

    //...
}
```

## Main menu

Nous allons utilisez un helper dans Twig pour afficher le menu principal. Ainsi dans le template base.html.twig vous allez écrire, à la place de l'include Twig déjà en place, qui vous supprimez, le helper suivant qui appelera une méthode publique **mainMenu**. Elle retournera le menu principal :

```php
{{render(controller('App\\Controller\\BarController:mainMenu',
  {
    'routeName' : app.request.attributes.get('_route')
}))}}

```

*Remarques: dans la méthode mainMenu vous devez récupérez les catégories "normales" et les passer à la vue.*

## Affichez les bières par catégorie

Vous allez maintenant affichez les bières par catégorie, en effet, dans le menu principal vous créez les liens vers les catégories affichant les bières de cette catégorie.

## Synthèse 

*Dans la suite vous pouvez utiliser le logiciel Dia pour modèliser les relations entre les tables.*

Vous allez implémenter une nouvelle entité Client elle permettra de faire un peu de statistique sur la consommation de bière(s) des clients. Cette table nous renseignera plus précisémenet sur le poids et la consommation de nos clients. Un client peut consommer plusieurs types de bières. Nous essayerons de regrouper ces informations sous forme d'un tableau sur une page de notre application.

Essayez de concevoir une entité Statistic qui permettra de regrouper les informations liées à la consommation de bière de nos clients, puis faites un schéma permettant de représenter ces relations.

Pour terminez nous aimerions également calculer les prix dépensés par nos clients pour l'achat de leurs bières.

### Intérêt et âge

Vous avez maintenant deux autres informations à gérer : chaque bière est notée par vos clients à l'aide d'un nouveau champ dans la table bière nommé **score**. D'un autre côté vous connaissez l'âge de chaque client (nouveau champ age à créer dans la table Client).

Modifiez les tables existantes en conséquence. Puis créez un nouveau tableau dans la partie statistique de notre application mettant en relation l'âge du client et le score qu'il a attribué à une bière donnée.

Pour vous aidez dans la réalisation de cette nouvelle fonctionnalité dans l'application nous vous proposons le schéma UML suivant :

![database schema](images/simplebar_03.png)
