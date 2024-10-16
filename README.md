# Automatisation

Louka Mellano 
Fabio Reveillard

# Informations diverses 

-Le fichier yml ne s'exécute pas sur notre propre machine mais sur les serveurs de github qui nous sont alloués pour effectuer les différents tests. C'est pour cela que nous ne voyons pas le fichier xml cobertura dans notre répository github mais nous pouvons voir son contenue avec un cat.


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

Nous avons rencontré une erreur de fichier manquant lors de la génération du rapport de couverture, car nous avions oublié d'ajouter la ligne suivante "<cobertura outputFile="log/cobertura.xml"/>" dans le fichier phpunit.xml. 
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

# Troisième partie { DEPLOIEMENT } 

