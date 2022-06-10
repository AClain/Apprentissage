Apprendre Laravel
===

![](https://i.imgur.com/rHgTcIF.png)

# Préface

Cette documentation est valide pour la version 9 (et supérieure) de Laravel allant avec la version 8.1 (et supérieure) de PHP.

# Sommaire

1. [Les bases](#Les-bases)
    1. [Pré-requis](#Pré-requis)
    2. [Installation](#Installation)
        1. [MacOS](#MacOS)
        2. [Linux](#Linux)
        3. [Windows](#Windows)
        4. [Composer](#Composer)
    3. [Architecture des fichiers](#Architecture-des-fichiers)
    4. [Le fichier `.env`](#Le-fichier-.env)
    5. [Controller](#Controller)
        1. [Controller - Bases](#Controller---Bases)
        2. [Réponse HTTP](#Réponse-HTTP)
        3. [Bonnes pratiques](#Bonnes-pratiques)
    6. [Routes](#Routes)
        1. [Routes - Bases](#Routes---Bases)
        2. [Lier à une méthode de Controller](#Lier-à-une-méthode-de-Controller)
        3. [Les paramètres d'url](#Les-paramètres-d'url)
        4. [Grouper les routes](#Grouper-les-routes)
        5. [Appliquer un middleware](#Appliquer-un-middleware)
    7. [Migration](#Migration)
        1. [Migration - Bases](#Migrations---Bases)
        2. [Convention de nommage](#Conventions-de-nommage)
        3. [Écrire les migrations](#Écrire-les-migrations)
        4. [Contraintes](#Contraintes)
        5. [Exécuter les migrations](#Exécuter-les-migrations)
        6. [Bonnes pratiques](#Bonnes-pratiques)
    8. [Model](#Model)
        1. [Attributs](#Attributs)
        2. [Définir les relations](#Définir-les-relations)
        3. [Créer une entité](#Créer-une-entité)
        4. [Mettre à jour une entité](#Mettre-à-jour-une-entité)
        5. [Supprimer une entité](#Supprimer-une-entité)
    10. [Collection](#Collection)
        1. [Méthodes](#Méthodes)
    12. [Seeder](#Seeder)
    13. [Validation](#Validation)
    14. [Logging](#Logging)
    15. [CLI](#CLI)
2. [Avancés](#Avancés)
    1. [Middleware](#Middleware)
    2. [Form Request](#Form-Request)
    3. [Resource](#Resource)
    4. [Refactoriser](#Refactoriser)
3. [Glossaire](#Glossaire)
4. [Sources et documentation](#Source-et-documentation)
    4. [Vidéos](#Vidéos)
    5. [Documentation officielle](#Documentation-officielle)

# Les bases

## Pré-requis

Afin d’utiliser Laravel de la meilleure manière, il faut d’abord installer:
- **PHP** (**>=7.4** pour des versions **inférieures à 9**, **>=8.1** pour la **version 9**);
- **Composer** (installer et gérer les dépendances;
- **Docker** (si l’on souhaite Dockeriser l’application);
- **Docker Desktop** (gérer les conteneurs/images Docker) ou **Docker Compose** pour - Linux

## [Installation](https://laravel.com/docs/9.x/installation)

L’installation reste quasiment identique pour Windows, MacOS ou Linux. Ici, "*example-app*" est le nom du dossier qui sera téléchargé et qui contiendra le squelette de l’application.

De base, ce dossier contiendra un squelette d’application Laravel avec quelques extensions dont:
- **Laravel Sail**: permet de Dockeriser une application Laravel;
- **Laravel Sanctum**: permet de sécuriser une application de type API;
- **Laravel Tinker**: facilite l’écriture de commande personnaliser "*artisan*"

### [MacOS](https://laravel.com/docs/9.x/installation#getting-started-on-macos)

`curl -s "https://laravel.build/example-app" | bash`

### [Windows](https://laravel.com/docs/9.x/installation#getting-started-on-windows)

`curl -s https://laravel.build/example-app | bash`

### [Linux](https://laravel.com/docs/9.x/installation#getting-started-on-linux)

`curl -s https://laravel.build/example-app | bash`

### [Avec Composer](https://laravel.com/docs/9.x/installation#installation-via-composer)

`composer create-project laravel/laravel example-app`

Une fois le dossier téléchargé, on peut s’y rendre et lancer l’application sur le port **8000** (port par défaut utilisé) avec:

`vendor/bin/sail up -d`

Il est conseillé de [configurer un alias](https://laravel.com/docs/9.x/sail#configuring-a-bash-alias) pour la commande `sail`.

La première fois, cela va créer les conteneurs Docker en fonction des services définis dans le `docker-compose.yml`, puis lancer les différents conteneurs et commandes nécessaires au fonctionnement de l’application.

Les différents ports mappés par Docker peuvent être modifié dans le fichier `.env` situé à la racine:

```
APP_PORT -> port de l’application
FORWARD_DB_PORT -> port pour MySQL
FORWARD_REDIS_PORT -> port pour Redis
FORWARD_MEILISEARCH_PORT -> port pour Meilisearch
FORWARD_MAILHOG_PORT -> port pour Mailhog
FORWARD_MAILHOG_DASHBOARD_PORT -> port le dashboard Mailhog
```

ou, si Docker n’est pas utilisé:

`php artisan serve`

Cela lance l’application sur le port 8000.

## [Architecture des fichiers](https://laravel.com/docs/9.x/structure)

```
.
├── app/
│   └── Console/
│       ├── Commands/
│       └── Kernel.php       -> Emplacement de la programmation des commandes (toutes les minutes/heures/mois, etc.)
│   ├── Http/
│   │   ├── Controllers/
│   │   ├── Middleware/
│   │   ├── Requests/
│   │   ├── Resources/
│   │   └── Kernel.php       -> Emplacement des règles pour les Middleware
│   └── Models/
├── config/                  -> Dossier contenant les fichiers de configuration, se basant sur le fichier .env et le helper config()
├── database/
│   ├── migrations/
│   └── seeders/
├── routes/
│   ├── api.php
│   └── web.php
├── storage/
│   ├── app/                 -> Dossier de stockages des médias (images, vidéos, etc.)
│   └── logs/
│       └──  laravel.log
├── vendor/                  -> Dossier contenant les dépendances du projet
├── .env
├── .env.example
├── .gitignore
├── artisan
├── composer.json
├── docker-compose.yml
├── LICENSE
└── README.md
```

## [Le fichier .env](https://laravel.com/docs/9.x/configuration#environment-configuration)

Le fichier .env situé à la racine est, de base, une copie du fichier `.env.example`. C’est dans ce fichier que se situent toutes les **variable d'environnement** dans l’application, qui ne doivent **pas apparaître publiquement** et qui peuvent changer **en fonction de chaque développeur**.

C’est notamment ici que l’on retrouve les identifiants pour la **connexion à la base de données**, qui doivent être indiqués avant de lancer l’application si Docker n’est pas utilisé.

## [Controller](https://laravel.com/docs/9.x/controllers)

### [Controller - Bases](https://laravel.com/docs/9.x/controllers#writing-controllers)

Un `Controller` est chargé de contenir les différentes méthodes que l'ont souhaitent exécutées sur les différents `Model` et doit être nommé, dans la plupart des cas, par rapport à celui-ci. Par exemple, si un `Model` "*User*" existe, toutes les actions possibles sur ce `Model` seront définies dans le `Controller` "*UserController*".

Il y a une convention quant au nom des méthodes dans un Controller:

- Afficher les informations d'une entité: `show()`;
- Enregistrer une nouvelle entité: `store()`;
- Mettre à jour une entité: `update()`;
- Supprimer une entité: `destroy()`

Une entité est une instance d'un `Model`, par exemple ici, `$user` est une instance du `Model` "*User*":

```php=
<?php
// app/Http/Controllers/UserController.php

use App\Models\User;

$user = User::find(1);
```

Avec le CLI `artisan` fourni avec Laravel, il est possible de créer les Controller directement avec une commande:

`sail artisan make:controller UserController`

ou sans Docker: `php artisan make:controller UserController`

Un squelette avec toutes les méthodes de bases sera alors créé.

### [Réponse HTTP](https://laravel.com/docs/9.x/responses)

De base, il est possible de renvoyer n'importe quel type de réponse, html, array, json, une `View` (Blade), une redirection, etc. Il est de bonne pratiques d'utiliser les helpers Laravel plutôt que de renvoyer directement un type de donnée.

Avec le helper `response()` il est possible de spécifier plusieurs éléments:

- La réponse en elle-même, qu'elle soit de type string, array ou JSON;
- Le code HTTP renvoyé;
- Les headers

Par exemple, pour renvoyer un tableau d'erreurs avec le code HTTP 400:

```php=
<?php
// app/Http/Controllers/UserController.php

public function store(User $user) {
    // ... code
    
    $errors = [
        'name' => 'Le nom n\'est pas au bon format'  
    ];
    
    return response($errors, 400);
}
```

Pour renvoyer du JSON, le helper `response()` peut être étendu avec la méthode `json()`, qui va s'occuper de transformer la donnée en premier paramètre en JSON lisible dans la réponse:

```php=
<?php
// app/Http/Controllers/UserController.php

use App\Models\User;

public function show(User $user)
{
    return response()->json(user);
}
```

Par défaut, le code HTTP renvoyé par `response()` et `json()` est **200**.

### Bonnes pratiques

Il faut toujours faire attention à la taille d'un Controller, s'il dépasse plusieurs centaines de lignes, il doit surement il y avoir du code qui ne doit pas être là. Un Controller doit se servir des différents types de classes à sa disposition afin d'effectué les différentes actions demandés. Par exemple, si le `Model` "*User*" est relié à plusieurs rôle, s'il l'on souhaite récupérer les "*User*" avec un rôle particulier, il parait logique de faire:

```php=
<?php
// app/Http/Controllers/UserController.php

use App\Models\User;

public function getWithRole(int $role_id)
{
    $users = User::where('role_id', $role_id)->get();
    
    return response()->json($users);
}
```

Cela peut paraître peu, mais ce type de requête (SQL) sera surement réutilisée à d'autres endroits. Il semble donc plus approprié d'écrire une méthode qui permet de récupérer tous les "*User*" avec comme paramètre l'id d'un "*Role*" dans une classe `Service` (type de classe non implémentée par Laravel) ou directement dans la classe du `Model` "*User*":

```php=
<?php
// app\Models\User.php

public static function getAllWithRole(int $role_id)
{
    return self::where('role_id', $role_id)->get();
}

```

Dans le `Controller`:

```php=
<?php
// app/Http/Controllers/UserController.php

use App\Models\User;

public function getWithRole(int $role_id)
{
    $users = User::getAllWithRole($role_id);
    
    return response()->json($users);
}
```

## [Routes](https://laravel.com/docs/9.x/routing)

### Routes - Bases

Le premier **point d'entrée** de l’application se situe dans les fichiers situés dans le dossier `routes`, à la racine. Ils servent à modéliser les différentes **routes** de l’application. Chaque fichier dans ce dossier est soumis à un ou plusieurs **middleware** par défaut.

Le fichier `web.php` sert surtout lorsque l'on souhaite développer une application full-stack avec Laravel et son moteur de template **Blade**.

Le fichier `api.php` sert lorsque l’on veut développer une **API**. Il ajoute le **préfixe** `/api` aux routes décrites à l’intérieur.

Si l’on souhaite créer une route qui réponde à la requête HTTP `GET` sur `/api/hello`, qui renvoie le texte "Hello world!", on utilisera le fichier `routes/api.php`:

```php=
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;

Route::get(‘/hello’, function () {
    return "Hello world!";
})->name(‘hello’);
```

La classe `Route` est utilisée pour décrire les différentes routes de notre application. Elle possède plusieurs **méthodes statiques** permettant de noter les différentes méthodes HTTP utilisées comme `GET`, `POST`, `PUT` ou `v`, avec leur méthode respective: `Route::get()`, `Route::post()`, `Route::put()` et `Route::delete()`.

Chacune de ses méthodes prend au moins **deux** paramètres, la première est la route que l'on souhaite modéliser. Le deuxième peut varier selon le cas d’utilisation. Dans le cas ci-dessus, on utilise une **fonction anonyme** qui retourne du texte simple.

Il est également important de **nommer** les routes afin de s’y retrouver et d’y faire référence dans le code dans d'autres contextes.

### [Lier à une méthode de Controller](https://laravel.com/docs/9.x/routing#route-group-controllers)

Une autre manière de lier une route à une action est d’indiquer le `Controller` ainsi que la méthode liés à cette route:

```php=
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController;

Route::get(‘/users/all’, [UserController::class, ‘all’])->name(‘users.all’);
```

Grâce à cette écriture, la route /users/all est liée au UserController et elle va chercher la méthode all de celui-ci.

### [Les paramètres dans l'url](https://laravel.com/docs/9.x/routing#route-parameters)

Il est possible de passer des **paramètres dynamiques** dans l’url via les routes que l’on définit. Par exemple, si l’on souhaite récupérer un "*User*" par son id et afficher ses informations:

```php=
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController;

Route::get(‘/users/{id}, [UserController::class, ‘show’])->name(‘users.show’);
```

dans le `Controller`:

```php=
<?php
// routes/api.php

use App\Models\User;

public function show(int $id)
{
    $user = User::find($id);

    return $user;
}
```

Une meilleure façon de le faire est d'utiliser la liaison entre une route et un modèle via le paramètre de l'url. Toujours dans le cas où l'on souhaite afficher les informations d'un "*User*":

```php=
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController;

Route::get(‘/users/{user}, [UserController::class, ‘show’])->name(‘users.show’);
```

dans le Controller:

```php=
<?php
// app/Http/Controllers/UserController.php

use App\Models\User;

public function show(User $user) {
    return $user;
}
```

Cette fois-ci, plutôt que d'aller récupérer l'utilisateur grâce à l'id passé dans l'url, Laravel le fait automatiquement. Il suffit de préciser dans la méthode que l'on souhaite avoir un "*User*" pour la variable `$user` plutôt qu'un `int` avec `$id`.

### [Grouper les routes](https://laravel.com/docs/9.x/routing#route-groups)

Pour éviter la répétition dans l’écriture de ses routes, il est possible de les grouper:

- Par prefix

```php=
<?php
// routes/api.php

use App\Http\Controllers\UserController;

Route::prefix(‘users’)->group(function () {
    Route::get(‘/’, [UserController::class, ‘index’])->name(‘users.index’);
    Route::post(‘/’, [UserController::class, ‘store’])->name(‘users.store’);
    Route::get(‘/{user}’, [UserController::class, ‘show’])->name(‘users.show’);
    Route::POST(‘/{user}’, [UserController::class, ‘update’])->name(‘users.update’);
    Route::DELETE(‘/{user}’, [UserController::class, ‘destroy’])->name(‘users.destroy’);
});
```

La méthode `prefix()` permet de préfixer toutes les routes avec un préfixe. Dans le cas ci-dessus, les routes suivantes seront créées:

```
GET /api/users
POST /api/users
GET /api/users/1
POST /api/users/1
DELETE /api/users/1
```

- Par `Controller`:

```php=
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController;

Route::controller(UserController::class)->group(function () {
    Route::get(‘/’, 'index')->name(‘users.index’);
    Route::post(‘/’, 'store')->name(‘users.store’);
    Route::get(‘/{user}’, 'show')->name(‘users.show’);
    Route::POST(‘/{user}’, 'update')->name(‘users.update’);
    Route::DELETE(‘/{user}’, 'destroy')->name(‘users.destroy’);
});
```

Dans ce cas, chaque Route définis ira chercher directement une méthode dans le "*UserController*". Le deuxième paramètre des fonctions type `Route::get()` n'ont plus besoin d'être un tableau contenant le `Controller` et la méthode, mais seulement la méthode sous forme de string.

### [Appliquer un middleware](https://laravel.com/docs/9.x/middleware#assigning-middleware-to-routes)

Un **Middleware** est une suite d'instructions exécutées avant l'appel d'une méthode liée à une route. Imaginons que nous ayons un `Middleware` vérifiant que l'utilisateur est bien connecté:

```php=
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;

Route::middleware(['authenticated'])->group(function () {
    // Routes accessible seulement si l'utilisateur est connecté
});
```

Le `Middleware` enregistré avec le nom "*authenticated*" sera alors exécuté avant les méthodes liés aux routes.

```php=
<?php
// routes/api.php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\UserController;

Route::controller(UserController::class)
    ->middleware(['admin'])
    ->group(function () {
        Route::get(‘/’, 'index')->name(‘users.index’);
        Route::post(‘/’, 'store')->name(‘users.store’);
        Route::get(‘/{user}’, 'show')->name(‘users.show’);
        Route::POST(‘/{user}’, 'update')->name(‘users.update’);
        Route::DELETE(‘/{user}’, 'destroy')->name(‘users.destroy’);
    });
```

Ici, les routes liées au `UserController` sont accéssiblent seulement si le `Middleware` "*admin*" le permet.

![](https://i.imgur.com/cIS0Dmo.png)

## [Migrations](https://laravel.com/docs/9.x/migrations)

### Migrations - Bases

Les fichiers de migrations sont là pour définir la structures des tables dans la base de données. Une migration va aussi définir un `Model`, il faut donc faire attention au nommage.

Pour commencer et générer une migration, on peut utiliser `artisan`:

`php artisan make:migration [nom_de_la_migration]`

### Conventions de nommage

Avant de créer un fichier de migration, il faut connaître les différentes conventions pour les noms de migration:

- Pour créer une table: `create_[nom_du_modèle_au_pluriel]_table`
Par exemple, si l'on veut un `Model` "*User*":
`create_users_table`

- Pour mettre à jour une table:
    - Si l'on souhaite ajoute une colonne: `add_[nom_de_la_colonne]_to_[nom_de_la_table]_table`
        Ajouter une colonne "*status_id*" à la table "*users*":
        `add_status_id_to_users_table`

    - Si l'on souhaite retirer une colonne: `remove_[nom_de_la_colonne]_from_[nom_de_la_table]_table`
        Retirer la colonne "*status_id*" de la table "*users*":
        `remove_status_id_from_users_table`

    - Si l'on souhaite effectuer plusieurs modifications: `alter_[nom_de_la_table]_table`

Pour créer un fichier de migration, il faut utiliser la commande `make:migration`:

`php artisan make:migration [nom_de_la_migration]`

Cela va générer une migration dans le dossier `database/migrations` et le nom du fichier suivra cette convention:

`[timestamp]_[nom_de_la_migration].php`

A savoir que lors de l'exécution des migrations, elles se feront dans l'ordre chronologique puis alphabétique.

### [Écrire les migrations](https://laravel.com/docs/9.x/migrations#tables)

Après génération d'un fichier de migration, une classe est créée avec un nom similaire. Cette classe étend de la classe `Migration` qui est nécessaire pour le fonctionnement de celle-ci:

```php=
<?php
// database/migrations/2022_06_10_555255_create_users_table.php

class CreateUsersTable extends Migration
{
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('users');
    }
}
```

Le nom utilisé dans les fonctions `create()` et `dropIfExists()` est généré à partir du nom de la migration et sera le nom de la table dans la base de données.

Les colonnes se définnissent grâce à la variable `$table` de la classe `Blueprint` qui possède tout un tas de méthode pour créer des colonnes. Chaque méthode résulte en une colonne avec plus ou moins de contraintes.

De base, la migration possède 3 colonnes:

- Une colonne `id` étant la PRIMARY KEY en AUTO INCREMENT et UNSIGNED, généré avec `id()`;
- Une colonne `created_at` qui, par défaut, est égal à l'heure et la date au moment où l'entité est créée, générée avec `timestamps()`;
- Une colonne `updated_at` qui se met à jour avec la date et l'heure au moment où l'entité est mis à jour, générée avec `timestamps()`

Il existe plusieurs types de colonnes, parmis les plus utilisés:

|         Méthode | Description                                              |
| ---------------:|:-------------------------------------------------------- |
| bigIncrements() | Pareil que id() avec un interval plus grand              |
|       boolean() | 0 ou 1                                                   |
|      dateTime() | Heure et date (format `{year}_{month}_{day}_{time}`)     |
|          date() | Date (format `{year}_{month}_{day}`                      |
|        double() | Pareil que float() avec un interval plus grand           |
|         float() | Nombre à virgule                                         |
|  foreignIdFor() | Colonne de relation avec un `Model` donné                |
|            id() | Colonne `id` en PRIMARY KEY, AUTO INCREMENT et UNSIGNED  |
|       integer() | Nombre                                                   |
| rememberToken() | Colonne utilisé pour l'authentification des utilisateurs |
|        string() | Chaîne de charactère ne dépassant pas 255                |
|          text() | Chaîne de charactère                                     |

### [Contraintes](https://laravel.com/docs/9.x/migrations#column-modifiers)

Il est possible de rajouter des contraintes sur ces colonnes. Pour les appliquer, il faut chaîner les méthodes. Par exemple, si l'on souhaite avoir une contrainte unique sur la colonne email de la table users:

```php=
<?php
// database/migrations/2022_06_10_356535_create_users_table.php

Schema::create('users', function (Blueprint $table) {
    $table->string('email')->unique();
});
```

Cela empêchera, lors d'une insertion en DB, la duplication sur la colonne email.

Il esxite plusieurs types de contraintes, les plus utilisées sont surement les contraintes `references()->on()` (contrainte pour les clés étrangères), `nullable()` pour indiquer d'une colonne peut valoir `null` et `default()` pour indiquer une valeur par défaut.

- `reference()->on()`

Imaginons une table **avatars** et une table **users** possédant une colonne **avatar_id** faisant référence à un "*Avatar*":

```php=
<?php
// database/migrations/2022_06_10_135642_create_avatarstable.php

Schema::create('users', function (Blueprint $table) {
    $table->text('src');
});
```

```php=
<?php
// database/migrations/2022_06_10_243563_create_users_table.php

use App\Models\Avatar;

Schema::create('users', function (Blueprint $table) {
    $table->foreignIdFor(Avatar::class)->references('id')->on('avatars');
});
```

Notez le nom des migrations, la table **users** faisant référence à la table **avatars**, il est donc important que celle-ci soit générée avant l'autre. C'est un point important à prendre en compte lors de la génération des tables.

Ici, on décrit une colonne **avatar_id** en BIGINT UNSIGNED grâce à la méthode foreignIdFor() (il est possible de changer le nom de la colonne générée par la méthode en passant un deuxième paramètre). Sur cette même colonne, on indique qu'elle fait référence à la table **avatars** sur la colonne **avatar_id**. Si l'on souhaite maintenant insérer une nouvelle entité "*User*", il faudra que l'id passé à la colonne **avatar_id** soit une référence à une entité existante dans la table **avatars**.

- `nullable()`

La contrainte `nullable()` permet d'avoir une colonne avec comme valeur `null`. Par défaut, si cette contrainte n'est pas renseignée, toutes les colonnes s'attendent à avoir une valeur et vont renvoyer une erreur lors de l'insértion si elle sont égales à `null`: `

```php=
<?php
// database/migrations/2022_06_10_243563_create_users_table.php

use App\Models\Avatar;

Schema::create('users', function (Blueprint $table) {
    $table->foreignIdFor(Avatar::class)->references('id')->on('avatars')->nullable();
});
```

En reprenant la migration ci-dessus, on peut rajouter la contrainte `nullable()` si la **avatar_id** afin d'indiquer qu'elle peut avoir une valeur `null`.

- `default()`

La méthode `default()` permet d'avoir une valeur par défaut pour une colonne:

```php=
<?php
// database/migrations/2022_06_10_243563_create_users_table.php

use App\Models\Avatar;

Schema::create('users', function (Blueprint $table) {
    $table->boolean('is_active')->default(false);
});
```

### [Exécuter les migrations](https://laravel.com/docs/9.x/migrations#running-migrations)

Une fois satisfait des migrations générées, on peut les exécuter avec la commande:

`php artisan migrate`

Il est possible de rajouter plusieurs option, comme `fresh` ou `seed`. La première stipule que l'on souhaite effacer toutes les données de la DB avant d'effectuer les migrations:

`php artisan migrate:fresh`

La deuxième option demande d'exécuter les `Seeder` après les migrations:

`php artisan migrate:fresh --seed`

### Bonnes pratiques

- Durant la phase de développement

La base de données utilise les seeders afin de tester les différentes parties de l'application. La DB subira donc des purges toutes les semaines voire tous les jours à des fins de tests. Il n'est donc pas nécessaire de recréer une migration lorsque l'on veut modifier une table, sinon le dossier devient vite surcharger et il est difficile de s'y retrouver.

En revanche, lors de la mise en production pour le client, les migrations ne peuvent plus être édités et de nouvelles doivent être créées, car les données en DB ne peuvent plus être supprimées.

- Nom des tables

Dans le cas d'un `Many to Many`, par exemple un "*User*" a plusieurs "*Role*" (et donc un même "*Role*" est lié à plusieurs "*User*"), la table considérée comme plus "au centre" sera en première. Dans le cas ci-dessus, la table "*User*" sera la plus "au centre", la table de relation sera donc nommée `user_roles`.

## [Model](https://laravel.com/docs/9.x/eloquent)

### [Générer un `Model`](https://laravel.com/docs/9.x/eloquent#generating-model-classes)

Les `Model` sont les principaux point d'entrée qui permettent d'intéragir avec la base de données. Ils possèdent plusieurs attributs et méthodes permettant de configurer la façon de créer, mettre à jour et supprimer les entités.

Pour créer un `Model` il faut utiliser la commande:

`php artisan make:model [nom_du_modèle]`

Un fichier sera alors créé dans le dossier `app/Models`:

`php artisan make:model User`

```php=
<?php
// app/Models/User.php

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    use HasFactory;
}
```

La classe "*User*" étend de la classe "*Model*". Cela permet d'hériter de différentes méthodes et attributs permettant de créer, modifier et supprimer une entité dans la base de données.

### Attributs

Il existe plusieurs attributs de classe permettant de configurer le comportement d'un `Model`

| Nom                          | Description                                         |
|------------------------------|-----------------------------------------------------|
| protected string $table      | Le nom de la table en base de données               |
| protected array $fillable    | Les colonnes pouvant être modifiés                  |
| protected array $hidden      | Les attributs cachés lors de la sérialisation       |
| protected array [$casts](https://laravel.com/docs/9.x/eloquent-mutators#attribute-casting)       | Le type (PHP) des colonnes lors de la sérialisation |
| protected array $appends     | Les attributs ajoutées lors de la sérialisation     |
| protected string $primaryKey | La clé primaire de la table en base de données      |

Imaginons la migration suivante:

```php=
<?php
// database/migrations/2022_06_10_243563_create_users_table.php

use App\Models\Avatar;

Schema::create('users', function (Blueprint $table) {
    $table->id();
    $table->foreignIdFor(Avatar::class)->references('id')->on('avatars');
    $table->string('email')->unique();
    $table->text('password');
    $table->boolean('is_active')->default(false);
    $table->timestamps();
});
```

A partir de cette migration, il est possible de faciliter le développement en renseignant plusieurs attributs et méthodes:

```php=
<?php
// app/Models/User.php

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Même si Laravel le fait normallement automatiquement, il est tout de même bon de 
     * renseigner la table concernée par le Model
     */
    protected $table = "users";
    
    /*
     * Ici, on renseigne le tableau des colonnes qui peuvent être modifiable, par
     * exemple, la colonne id n'y figure pas, elle ne pourra donc pas être modifé
     */
    protected $fillable = ['avatar_id', 'email', 'password', 'is_active'];
    
    /*
     * Ce tableau a pour but de cacher certaines colonnes lors de l'affichage
     * d'une entité, ici, la colonne password ne sera pas dans le tableau
     * de valeurs après la sérialization
     */
    protected $hidden = ['password'];
    
    /*
     * Dans certains cas, nous ne voulons pas avoir une données sous la forme
     * par défaut lors de la sérialization. Pour le cas de la colonne is_active,
     * on spécifie ici que l'on souhaite que la valeur soit forcémment de
     * type boolean
     */
    protected $casts = [
        'is_active' => 'boolean'
    ];
```

### [Définir les relations](https://laravel.com/docs/9.x/eloquent-relationships)

En reprenant le même fichier `Model` et migration ci-dessus et en ajoutant celui-ci:

```php=
<?php
// database/migrations/2022_06_10_123573_create_avatars_table.php

Schema::create('avatars', function (Blueprint $table) {
    $table->id();
    $table->text('link');
    $table->timestamps();
});
```

On peut générer le `Model` correspondant:

```php=
<?php
// app/Models/Avatar.php

use Illuminate\Database\Eloquent\Model;

class Avatar extends Model
{
    protected $table = "avatars";
    protected $fillable = ['link'];
}
```

La relation entre "*Avatar*" et "*User*" est de type One to One, un "*Avatar*" est relié à un "*User*" et inversemment, un "*User*" possède un "*Avatar*". Cette relation doit être définie au niveau des deux `Model`:

```php=
<?php
// app/Models/User.php

use Illuminate\Database\Eloquent\Model;
use App\Models\Avatar;

class User extends Model
{
    public function avatar()
    {
        $this->hasOne(Avatar::class, 'id', 'avatar_id');
    }
}
```

```php=
<?php
// app/Models/Avatar.php

use Illuminate\Database\Eloquent\Model;
use App\Models\User;

class Avatar extends Model
{
    public function user()
    {
        $this->belongsTo(User::class, 'avatar_id', 'id');
    }
}
```

Au niveau du `Model` "*User*", on peut définir une méthode au nom de la relation que l'on souhaite définir. Dans ce cas, un "*User*" a un "*Avatar*", donc on définis la méthode `avatar()`. Cette méthode doit retourner une relation, il existe plusieurs types de relation pour tous les cas:

| Nom             | Description                                                                 |
|-----------------|-----------------------------------------------------------------------------|
| hasOne()        | Dans le cas d'une relation One to One, un **User** a un **Avatar**          |
| hasMany()       | Dans le cas d'une relation One to Many, un **Post** a plusieurs **Comment** |
| belongsTo()     | L'inverse de la relation d'un One to One ou One to Many                     |
| belongsToMany() | Dans le cas d'un Many to Many                                               |

Le premier paramètre de ces méthodes est toujours la classe de relation.

#### One to One

- `hasOne($relation, $foreignKey, $ownerKey)`

Le deuxième paramètre est la colonne clé étrangère de la relation. Le troisième paramètre est la clé primaire de la relation.

Dans le cas d'un "*User*" qui a un "*Avatar*", la clé étrangère sera `users.avatar_id` et la clé primaire `avatars.id`.

Exemple: voir code ci-dessus.

Cela permet la syntaxe: `$user->avatar->link` pour récupérer la valeur du champ "*link*" de l'entité "*Avatar*" correspondante à l'entité "*User*".

#### One to Many

- `hasMany($relation, $foreignKey, $onerKey)`

Le deuxième paramètre est la colonne clé étrangère de la relation. Le troisième paramètre est la clé primaire de la relation.

Dans le cas d'un "*Post*" qui a plusieurs "*Comment*", la clé étrangère sera `comment.post_id` et la clé primaire `post.id`.

Exemple:

```php=
<?php
// app/Models/Post.php

use Illuminate\Database\Eloquent\Model;
use App\Models\Comment;

class Post extends Model
{
    public function comments()
    {
        $this->hasMany(Comment::class, 'post_id', 'id');
    }
}
```

```php=
<?php
// app/Models/Post.php

use Illuminate\Database\Eloquent\Model;
use App\Models\Post;

class Comment extends Model
{
    public function post()
    {
        $this->belongsTo(Post::class, 'post_id', 'id');
    }
}
```

Cela nous permet la syntaxe: `$post->comments` pour récupérer tout les "*Comment*" d'un "*Post*".

#### Many to Many

- `belongsToMany($relation, $relationTable, $localKey, $foreignKey)`

Le deuxième paramètre est la table qui relie les deux relations. Le troisième paramètre est la colonne faisant référence au `Model` courant. Le dernier paramètre est la colonne faisant référence au `Model` inverse.

Dans le cas d'un "*User*" qui a plusieurs "*Role*" et où les "*Role*" peuvent appartenir à plusieurs "*User*", la table de relation sera `user_roles` et les méthodes de relations seront:

```php=
<?php
// app/Models/User.php

use Illuminate\Database\Eloquent\Model;
use App\Models\Role;

class User extends Model
{
    public function roles()
    {
        $this->belongsToMany(Role::class, 'role_users', 'user_id', 'role_id');
    }
}
```
```php=
<?php
// app/Models/Role.php

use Illuminate\Database\Eloquent\Model;
use App\Models\User;

class Role extends Model
{
    public function users()
    {
        $this->belongsToMany(User::class, 'role_users', 'role_id', 'user_id');
    }
}
```

Notez les troisième et quatrième paramètres qui ne sont pas dans le même ordre pour les deux méthodes.

Il faudra égalemment créer le `Model` "*UserRole*" et la migration correspondante:

```php=
<?php
// database/migrations/2022_06_10_234554_create_user_roles_table.php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
use App\Models\User;
use App\Models\Role;

Schema::create('role_users', function (Blueprint $table) {
    $table->foreignIdFor(User::class)->references('id')->on('users');
    $table->foreignIdFor(Role::class)->references('id')->on('roles');
});
```

Cela permet la syntaxe: `$user->roles` pour récupérer la `Collection` de "*Role*" correspondante à l'entité "*User*".

### [Créer une entité](https://laravel.com/docs/9.x/eloquent#inserts)

Une entité est la représentation d'un `Model` sous forme de variable.

Pour créer une entité, il y a deux manières de faire.

- Instancier un nouveau `Model` de type "*User*"

```php=
<?php

use App\Models\User;
use Illuminate\Support\Facades\Hash;

$user = new User();
$user->email = "";
$user->first_name = "";
$user->last_name = "";
$user->password = Hash::make("");
$user->save();
```

Avec cette solution, on instancie un nouveau "*User*" avec `new` puis on assigne les différents attributs avec leur valeur respective. Et enfin, on sauvegarde/persiste l'entité dans la base de données avec la méthode `save()`.

- Utiliser la méthode `create()` héritée de `Model`

```php=
<?php

use App\Models\User;
use Illuminate\Support\Facades\Hash;

$user = User::create([
    'email' => "",
    'first_name' => "",
    'last_name' => "",
    'password' => Hash::make("")
]);
```

Cette solution plus lisible et optimal utilise la méthode `create()` qui prend en paramètre un tableau associatif contenant les valeurs des attributs du `Model` concerné. La méthode va ensuite sauvegarder / persister l'entité et retourner le "*User*" créé.

### [Récupérer une entité](https://laravel.com/docs/9.x/eloquent#retrieving-models)

Il est possible de récupérer une ou plusieurs entités à partir d'un `Model`.

Pour cela, il faut utiliser une syntaxe renssemblant à une instruction type MySQL et construire une requête avec nos contraintes.

Il y a trois parties importantes dans cette requêtes:

- **Quoi ?**

D'abord, il faut définir ce que l'on veut récupérer. Cela se fait de différentes manières.

On peut récupérer une entité par sa clé primaire directement en utilisant la méthode `find()`:

```php=
<?php

use App\Models\User;

$user = User::find(1);
```

En assumant que la clé primaire est la colonne nommée `id`, alors la requête sera équivalente à: "prend le User qui a comme id 1". La valeur retournée sera un "*User*".

On peut également indiquer plusieurs conditions avant de récupérer notre entité. Avec les différentes méthodes de type `where`:

| Nom                                                              | Description                                                                |
|------------------------------------------------------------------|---------------------------------------------------------------------------|
| where($colonne, $condition)                                      | Applique la condition voulue sur la colonne indiquée                      |
| whereIn($colonne, $tableau)                                      | La valeur de la colonne doit se trouver dans le tableau                   |
| orWhere($colonne, $condition)                                    | Applique une condition optionnelle sur la colonne indiquée                |
| whereBetween($colonne, $tableau)                                 | La valeur de la colonne doit se trouver entre les deux valeurs du tableau |
| whereNull($colonne, $tableau) / whereNotNull($colonne, $tableau) | La valeur de la colonne doit être null / ne doit pas être null            |
| whereColunm($colonne1, $colonne2)                                | Les valeurs des deux colonnes doivent être égales                         |

- **Comment ?**

Une fois les conditions indiquées, on peut appliquer plusieurs [filtres](https://laravel.com/docs/9.x/queries#ordering-grouping-limit-and-offset) avant de récupérer le résultat:

| Nom                       | Description                                     |
|---------------------------|-------------------------------------------------|
| orderBy($colonne, $order) | Tri selon la colonne indiquée ('ASC' ou 'DESC') |
| take($nombre)             | Renvoie un certain nombre de résultat           |
| skip($nombre)             | Ignore un certain nombre de résultat            |
| limit($nombre)            | Même principe que take()                        |
| offset($nombre)           | Même principe que skip()                        |
| latest()                  | Tri par date (plus récent en premier)           |
| oldest()                  | Tri par date (plus ancien en premier)           |

- **Combien ?**

Une fois la requête construite, il y a deux moyens de récupérer les résultats.

On récupére tous les résultats en utilisant la méthode `get()`:

```php=
<?php

use App\Models\User;

$users = User::where('is_active', true)->orWhere('status', '>', 1)->limit(50)->get();
```

Dans ce cas la valeur de retour stoquée dans `$users` sera une `Collection` de "*User*".

On récupére le premier résultat en utilisant la méthode `first()`:

```php=
<?php

use App\Models\User;

$user = User::where('is_active', true)->orWhere('status', '>', 1)->limit(50)->first();
```

Dans ce cas la valeur de retour stoquée dans `$user` sera une entité de la classe "*User*".

Si l'on souhaite récupérer toutes les entités d'un `Model`, il y la méthode `all()`:

```php=
<?php

use App\Models\User;

$users = User::all();
```

### [Mettre à jour une entité](https://laravel.com/docs/9.x/eloquent#updates)

Tout comme la sauvegarde / persistance d'une entité, il y a deux façon de faire:

```php=
<?php

use App\Models\User;

$user = User::find(1);
$user->email = "new.email@gmail.com";
$user->save();
```

Récupérer l'entité, mettre à jour les colonnes voulues puis persister les changements avec la méthode `save()`, ou:

```php=
<?php

use App\Models\User;

$user = User::find(1);
$user->update([
    'email' => "new.email@gmail.com"
]);
```

récupérer l'entité et utiliser la méthode `update()` qui prend en paramètre un tableau associatif contenant les valeurs des attributs du `Model` "*User*". Dans les deux cas, les méthodes `save()` et `update()` vont **persister** l'entité et retourner **true** si l'entité a bien été sauvegardée, **false** dans le cas contraire.

### [Supprimer une entité](https://laravel.com/docs/9.x/eloquent#deleting-models)

```php=
<?php

$user = User::find(1);

$user->delete();
```

La méthode `delete()` renvoie **true** si l'entité a bien été supprimée, **false** dans le cas contraire.

## [Collection](https://laravel.com/docs/9.x/collections)

La classe `Collection` de Laravel permet d'améliorer la gestion des **tableaux** PHP. Dans la plupart des cas,  l'utilisation d'une Collection sera plus appropriée qu'un tableau. Elle fonctionne particulièrement bien avec les **tableaux associatifs**.

### [Méthodes](https://laravel.com/docs/9.x/collections#available-methods)

| Nom                                       | Description                                                                                                    |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| **all**()                                 | Retourne le tableau contenu dans la Collection                                                                 |
| **collect**($tableau)                     | Le helper `collect()` permet de créer une nouvelle Collection à partir d'un tableau                            |
| **count**()                               | Retourne le nombre d'éléments contenu dans la Collection                                                       |
| **each**($callback)                       | Fonctionne de la même manière qu'un foreach()                                                                  |
| **first**()                               | Retourne le premier élément de la Collection                                                                   |
| **firstWhere**($key, $condition, $valeur) | Retourne le premier élément qui valide la condition ($condition -> "=", ">=", "<=", etc.)                      |
| **get**($key)                             | Renvoie l'élément à l'index $key                                                                               |
| **isEmpty**()                             | Retourne true si la Collection est vide, false sinon                                                           |
| **last**()                                | Retourne le dernier élément de la Collection                                                                   |
| **map**($callback)                        | Fonctionne de la même manière qu'un array_map()                                                                |
| **mapWithKeys**($callback)                | Fonctionne de la même manière qu'un map() à l'exception qu'il réduit chaque élément à un tableau clé -> valeur |
| **random**()                              | Renvoie un élément aléatoire de la Collection                                                                  |
| **shuffle**()                             | Mélange les éléments de la Collection de façon aléatoire                                                       |
| **contains**($callback)                   | Renvoie true si la Collection contient un élément valide selon le $callback, false sinon                       |
| **toArray**()                             | Converti la Collection en array PHP                                                                            |
| **unique**()                              | Retire les doublons de la Collection                                                                           |
| **where**($key, $valeur)                  | Fonctionne de la même manière que la méthode where() de Model                                                  |
| **whereIn**($key, $tableau)               | Fonctionne de la même manière que la méthode whereIn() de Model                                                |
| **whereBetween**($key, $tableau)          | Fonctionne de la même manière que la méthode whereBetween() de Model                                           |

## [Seeder](https://laravel.com/docs/9.x/seeding)

## [Validation](https://laravel.com/docs/9.x/validation)

## [Logging](https://laravel.com/docs/9.x/logging)

## [CLI](https://laravel.com/docs/9.x/artisan)

# Avancés

## [Middleware](https://laravel.com/docs/9.x/middleware)

## [Form Request](https://laravel.com/docs/9.x/validation#form-request-validation)

## [Resource](https://laravel.com/docs/9.x/eloquent-resources)

## Refactoriser

Une refacto simple à faire est d'identifier toutes les fois où l'on effectue une requête `where()` sur la colonne `id`. Dans la plupart des cas, la colonne `id` est la clé primaire de la table, il est donc possible d'utiliser la fonction `find()` à la place:

```php=
<?php

$user = User::where('id', $id)->first();

$user = User::find($id);
```

Lorsque l'on souhaite référencer une certaine entité grâce à sa clé primaire dans l'url d'une route, par exemple `/users/{id}`, il est commun d'écrire dans le Controller:

```php=
<?php

Route::get('/users/{id}', [UserController::class, 'show'])->name('users.show');

public function show(int $id)
{
    $user = User::find($id);
    // ...
}
```

Laravel offre la possibilité de récupérer directement l'entité grâce aux liaisons implicites:

```php=
<?php

Route::get('/users/{user}', [UserController::class, 'show'])->name('users.show');

public function show(User $user)
{
    // ...
}
```

# Glossaire

- **Controller**

Dans l'architecture **MVC**, le 'C' signifie '**Controller**'. Un contrôleur est un fichier qui contrôle le comportement à avoir lors de la reception d'une requête provenant des routes. Dans Laravel, un contrôleur se trouve dans le répertoire `app/Http/Controllers`, tous les contrôleurs créés doivent se trouver dans ce répertoire.

- **Model**

Dans l'architecture **MVC**, "M" signifie "**Modèle**". Un modèle est essentiellement un moyen d'interroger des données depuis et vers une table de la base de données. Laravel fournit un moyen simple de le faire en utilisant **Eloquent ORM** (Object-Relational Mapping). Chaque table a un modèle pour interagir avec la table.

- **Collection**

Une collection fonctionne comme un tableau PHP, mais elle est plus pratique. Elle permet de créer une **chaîne de méthodes** pour mapper ou réduire des tableaux. La classe **Collection** provient de [Illuminate\Support\Collection](https://laravel.com/api/9.x/Illuminate/Support/Collection.html).

- **Seeder**

Laravel offre la possibilité de "seeder" (remplir avec de fausses données) la base de données en utilisant des classes **Seeder**. Toutes ces classes sont stockées dans le répertoire `database/seeders`. Par défaut, une classe **DatabaseSeeder** est définie par défaut. À partir de cette classe, on peut utiliser la méthode `call()` pour exécuter d'autres classes **Seeder**, dans l'ordre que l'on souhaite.

# Sources et documentation

## Vidéos

- [FR] [Playlist - Apprendre Laravel (Grafikart)](https://youtube.com/playlist?list=PLjwdMgw5TTLUCpXVEehCHs99N7IWByS3i) 
    - Bien que pour la version 5, certaines vidéos restent d'actualités et permettent de comprendre les principaux concepts de Laravel

- [FR] [Playlist - Tutoriels Laravel (Nord Coders)](https://youtube.com/playlist?list=PLeeuvNW2FHVi0scoRONNySMCanIvAZ9dH)
    - Chaîne quasiment dédiée à Laravel

- [EN] [Chaîne - LaravelDaily](https://www.youtube.com/c/LaravelDaily)
    - Chaîne dédiée à Laravel. Je recommande notamment pour ses vidéos de refactorization de code

- [EN] [Chaîne - Laracasts](https://www.youtube.com/c/Laracastsofficial)
    - En plus de possèder un forum complet dédié à Laravel, Laracasts possède une chaîne Youtube

- [EN] [Vidéo - Laravel 9](https://www.youtube.com/watch?v=GwunZ5sH2P8)

## Documentation officielle

- [Laravel 9.x](https://laravel.com/docs/9.x)
    - Documentation officielle

- [Namespaces API](https://laravel.com/api/9.x/)
    - La documentation ne répond parfois pas à certains cas d'utilisation, il est donc nécessaire de se renseigner sur une classe en particulier afin d'en apprendre plus et découvrir si notre problème est solvable