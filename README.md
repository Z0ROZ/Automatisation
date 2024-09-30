# Automatisation

Louka Mellano 
Fabio Reveillard



# Première partie { PHPUNIT }

Tout d'abord, nous avons rencontré des problèmes en passant par les dépendances et l'installation standard de PHPUnit. Pour contourner ces problèmes, nous avons choisi d'installer manuellement PHP 8.1 ainsi que toutes les extensions nécessaires afin de garantir le bon fonctionnement des tests. Nous avons également modifié l'installation de phpunit et du composer nous lançons directement une commande. 

## A-Configuration PHP
Nous utilisons l'action GitHub suivante pour installer PHP 8.1 avec les extensions requises :
```
    - name: Set up PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'  # Change to your PHP version
        extensions: gd, sqlite3, dom, json, libxml, mbstring, tokenizer, xml ,xmlwriter, zip
```

## B-Installation des dépendances
Nous installons les dépendances PHP via Composer sans suggestions supplémentaires ni affichage des progrès :*
```
  - name: 📝 Install dependencies
      run: composer install --no-progress --no-suggest
```

## C-Exécution des tests PHPUnit
Enfin, nous exécutons les tests avec une couverture de code via PHPUnit :
```
 - name: 🔨 Run PHPUnit tests with coverage
      run: vendor/bin/phpunit --coverage-text
```

# Deuxième partie { COVERAGE } 

```
- name: Code Coverage Summary Report
      uses: irongut/CodeCoverageSummary@v1.3.0
      with:
        filename: log/cobertura.xml
        badge: true
        format: markdown
        output: both
```
On définit le ‘name’ de l’action, c'est-à-dire le nom qui serra affiché lors du rapport. Ensuite, on définit le ‘uses’ avec ‘irongut/CodeCoverageSummary@v1.3.0‘ pour utiliser l’outil de couverture de code. Enfin, on précise le fichier de test xml qu’il faut générer au préalable

On peut éventuellement ajouter des paramètres comme : 
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

