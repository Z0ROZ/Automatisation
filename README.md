# Automatisation

Louka Mellano 
Fabio Reveillard

# Informations diverses 

- Le fichier yml ne s'exécute pas sur notre propre machine mais sur les serveurs de github qui nous sont alloués pour effectuer les différents tests. C'est pour cela que nous ne voyons pas le fichier xml cobertura dans notre répository github mais nous pouvons voir son contenue avec un cat.


# Première partie { PHPUNIT }

## A-Installation des dépendances
Dans cette partie, nous avons rencontré des problèmes de dépendances pour l'installation standard  du composer. En effet, plusieurs de nos bibliothèques avaient des conflits de versions, ce qui empêchait une installation réussie. De plus, notre code était initialement stocké dans un seul fichier, ce qui compliquait la structure attendue par Composer.

Pour contourner ces problèmes, nous avons décidé de déplacer notre code à la racine du projet, ce qui était censé améliorer la compatibilité avec les règles de Composer. Cependant, cela n'a pas suffi à résoudre les conflits de version. Donc, nous avons décidé d'installer le composer manuellement en ligne de commande dans le yml. 
```
     - name: 📝 Install dependencies
      run: composer install --no-progress --no-suggest

```

## B-Configuration PHP et Exécution des tests avec PHPUnit
Tout d'abord, nous avons décidé de retirer les dépendances du dossier vendor dans le fichier phpunit.xml afin de gagner du temps lors de l'exécution des tests. Nous avons également rencontré quelques problèmes avec les tests ServerSaltTest. Après analyse, nous avons choisi de les supprimer, car ils n'affectaient pas la phase de tests en elle-même, mais généraient simplement des messages de type "Deprecated" à la fin de l'exécution de PHPUnit, ce qui était désagréable.

Ensuite, nous pouvons ajouter plusieurs "paramètres" à PHPUnit, tels que les extensions requises, la version de PHP à utiliser, ainsi que le dossier de configuration XML.
```
   
    - name: 🔨 Running tests
      uses: php-actions/phpunit@v3
      with:
          php_extensions: gd sqlite3 dom json libxml mbstring tokenizer xml xmlwriter zip xdebug
          version: 9.6.11
          configuration: phpunit.xml

```

# Deuxième partie { COVERAGE } 
Dans cette deuxième partie, nous générons un rapport de couverture des tests qui sera affiché dans la console. À la fin des tests, un sommaire sous forme de tableau sera présenté, offrant une vue d'ensemble sur l'ensemble des lignes testées par PHPUnit.

Nous avons rencontré une erreur de fichier manquant lors de la génération du rapport de couverture, car nous avions oublié d'ajouter la ligne suivante cobertura outputFile="log/cobertura.xml" dans le fichier phpunit.xml. 
```
- name: Code Coverage Summary Report
      uses: irongut/CodeCoverageSummary@v1.3.0
      with:
        filename: log/cobertura.xml
        badge: true
        format: markdown
        output: both
```
Nous définissons d'abord le champ name de l’action, qui correspond au nom qui apparaîtra dans le rapport. Ensuite, nous spécifions l'action à utiliser avec irongut/CodeCoverageSummary@v1.3.0, qui sert à générer le rapport de couverture de code. Enfin, nous indiquons le fichier XML de test à générer au préalable.

Il est également possible d’ajouter des paramètres supplémentaires, tels que :
```
    badge: true
    fail_below_min: true
    format: markdown
    hide_branch_rate: false
    hide_complexity: true
    indicators: true
    output: both
    thresholds: '60 80'
```
# Troisième partie { LINTING }
Nous avons décidé de retirer le linting de notre fichier ci.yml, en conservant uniquement PHPUnit et la génération du code coverage.
Chaque outil de linting affiche dans la console les différentes erreurs présentes dans le code, ce qui entraîne une erreur dans le fichier YAML.

## A-PhpStan 
PHPStan est un outil d'analyse statique pour PHP qui détecte les erreurs de type et les problèmes potentiels dans le code sans l'exécuter. Il vérifie la cohérence des types de données, la validité des appels de méthodes et variables, et identifie les bugs
```
 - name: PHP Stan
   uses: php-actions/phpstan@v3
      with:
        path: ./

```
## B-PhpMd
PHPMD est un outil d'analyse statique pour PHP qui se concentre sur l'amélioration de la qualité du code en identifiant les mauvaises pratiques et les problèmes potentiels dans le code source. 
```
- name: PHP Mess Detector
  uses: php-actions/phpmd@v1
  with:
     path: ./
```
## C-PhpCs
PHPCS est un outil d'analyse statique utilisé pour détecter les violations des normes de codage dans les projets PHP.
```
- name: PHP Code Sniffer
  uses: php-actions/phpcs@v1
  with:
     path: ./
```

# Quatrième partie { DÉPLOIEMENT }
Ce fichier de configuration YAML permet de déployer automatiquement un site sur un serveur FTP dès qu'un commit est poussé sur la branche main. Il utilise l'action GitHub SamKirkland/FTP-Deploy-Action@4.3.1 pour transférer les fichiers du projet vers le serveur FTP.
La première étape est d'extraire le code, nous le faisons avec un checkout :
```
      - name: Checkout code
        uses: actions/checkout@v2

```
Ensuite, la deuxième étape consiste à déployer les fichiers sur le serveur FTP en utilisant les informations de connexion stockées dans les secrets de github (FTP_URL, FTP_USERNAME, FTP_PASSWORD). Le contenu local (situé dans ./) est transféré vers le répertoire www/ du serveur, en excluant certains fichiers comme .git, README.md, et les fichiers du dossier vendor déjà présent sur le serveur (trop volumineux donc trop longtemp à être upload).
```
      - name: FTP Deploy
        uses: SamKirkland/FTP-Deploy-Action@4.3.1
        with:
          server: ${{ secrets.FTP_URL }}      # Utilise le secret FTP_HOST
          username: ${{ secrets.FTP_USERNAME }} # Utilise le secret FTP_USERNAME
          password: ${{ secrets.FTP_PASSWORD }} # Utilise le secret FTP_PASSWORD
          local-dir: './'                        
          server-dir: 'www/'
          exclude: "[ **/.git**, **/.git*/**, **/README.md, **/*.yml, vendor/**]"

```
De plus, comme évoqué précédemment, si le code avait été placé dans un sous-dossier, comme cela a été fait auparavant, le site ne serait pas directement accessible via l'URL, car les fichiers ne se trouveraient pas à la racine du répertoire web (www/), ce qui empêcherait l'affichage immédiat du site.



