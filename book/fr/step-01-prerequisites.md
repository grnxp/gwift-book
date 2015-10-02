Avant propos
============

Avant de démarrer le développement, il est nécessaire de passer un peu de temps sur la configuration de l'environnement.

Nous allons utiliser [Python](https://www.python.org/), disponible sur la majorité des distributions Linux, ainsi que sur MacOS, dans des versions parfois différentes. Pour les utilisateurs de Windows, il sera sans doute nécessaire d'installer une version de l'interpréteur et de configurer la variable *PATH* pour votre utilisateur. Ajoutez-y `virtualenv` afin de créer un [environnement virtuel]() et les prérequis seront remplis.

Les morceaux de code seront développés pour Python3.4+ et nécessiteront peut-être quelques adaptations pour fonctionner sur une version antérieure.

Remarque : les commandes qui seront exécutés dans ce livre le seront depuis un shell sous linux. Certaines devrons donc être adaptés si vous êtes dans un autre environnemnet.

Création de l'environnement
---------------------------

Nous commençons par créer le répertoire du projet, à savoir `gwift-project`.

```shell
$ mkdir gwift-project
$ cd gwift-project
```

Ensuite, dans ce répertoire, nous créons notre répertoire d'environnement virtuel pour python avec `virtualenv`. Ce comportement a énormément d'avantages, notamment:

 * Isolation du code
 * Création d'un containeur pour les dépendances
 * Indépendances des librairies tierces déjà présentes sur la machine.

Lancez `virtualenv gwift-env` (ou le chemin vers le binaire de virtualenv à utiliser).

```shell
$ virtualenv gwift-env
New python executable in gwift-env/bin/python3
Also creating executable in gwift-env/bin/python
Installing setuptools, pip...done.
```

Ceci créera l'arborescence de fichiers suivante, qui peut à nouveau être un peu différente en fonction du système d'exploitation:

```shell
$ ls gwift-env
Include/ Lib/ Scripts/
```

Nous pouvons ensuite l'activer grâce à la commande `source gwirt-env/bin/activate`.

```shell
$ source gwirt-env/bin/activate
```

A présent, tous les binaires présents dans cet environnement prendront le pas sur les binaires du système. De la même manière, une variable *PATH* propre est définie et utilisée, afin que les librairies Python soient stockées dans le répertoire `gwift-env/Lib/site-packages/`. C'est notamment ici que nous retrouverons le code-source de Django, ainsi que des librairies externes une fois que nous les aurons installées.

Django
------

Comme l'environnement est activé, on peut à présent y installer Django. La librairie restera indépendante du reste du système, et ne polluera pas les autres projets.

C'est parti: `pip install django`!

```shell
$ pip install django
Collecting django
  Downloading Django-1.8.4-py2.py3-none-any.whl (6.2MB)
100% |################################| 6.2MB 91kB/s  eta 0:00:01
Installing collected packages: django
Successfully installed django-1.8.4
```

Les commandes de création d'un nouveau site sont à présent disponibles, la principale étant `django-admin startproject`. Par la suite, nous utiliserons `manage.py`, qui constitue un *wrapper* autour de `django-admin`.

Pour démarrer notre projet, nous lançons `django-admin startproject gwift`.

```shell
$ django-admin startproject gwift
```

Cette action aura pour effet de créer un nouveau dossier `gwift`, dans lequel on trouve la structure suivante:

```shell
$ tree gwift
gwift
|-- gwift
|   |-- __init__.py
|   |-- settings.py
|   |-- urls.py
|   |-- wsgi.py
|-- manage.py

```

 * `settings.py` contient tous les paramètres globaux à notre projet.
 * `urls.py` contient les variables de routes, les adresses utilisées et les fonctions vers lesquelles elles pointent.
 * `wsgi.py` contient la définition de l'interface [WSGI](https://en.wikipedia.org/wiki/Web_Server_Gateway_Interface), qui permettra à votre serveur Web (Nginx, Apache, ...) de faire un pont vers votre projet.

Gestion des dépendances
-----------------------

Comme nous venons d'ajouter une dépendance à notre projet, nous allons créer un fichier reprenant tous les dépendances de notre projet. Ceux-ci sont placés normalement dans un fichier `requirements.txt`. Dans un premier temps, ce fichier peut être placé directement à la racine du projet, mais on préférera rapidement le déplacer dans un sous-répertoire spécifique (`requirements`), afin de grouper les dépendances en fonction de leur utilité: 

 * `base.txt`
 * `dev.txt`
 * `staging.txt`
 * `production.txt`

Au début de chaque fichier, il suffira d'ajouter la ligne `-r base.txt`, puis de lancer l'installation grâce à un `pip install -r <nom du fichier>`. De cette manière, il est tout à fait acceptable de n'installer `flake8` et `django-debug-toolbar` qu'en développement par exemple.  Dans l'immédiat, ajoutez simplement `django` dans le fichier `requirements/base.txt`.

```shell
$ mkdir requirements
$ echo django >> requirements/base.txt
```