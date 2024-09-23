# Automatisation

Louka Mellano 
Fabio Reveillard



## Première partie { PHPUNIT }

Tout d'abord, nous avons rencontré des problèmes en passant par les dépendances et l'installation standard de PHPUnit. Pour contourner ces problèmes, nous avons choisi d'installer manuellement PHP 8.1 ainsi que toutes les extensions nécessaires afin de garantir le bon fonctionnement des tests. Nous avons également modifié l'installation de phpunit et du composer nous lançons directement une commande. 

#Configuration PHP
Nous utilisons l'action GitHub suivante pour installer PHP 8.1 avec les extensions requises :
    - name: Set up PHP
      uses: shivammathur/setup-php@v2
      with:
        php-version: '8.1'  # Change to your PHP version
        extensions: gd, sqlite3, dom, json, libxml, mbstring, tokenizer, xml ,xmlwriter, zip 

#Installation des dépendances
Nous installons les dépendances PHP via Composer sans suggestions supplémentaires ni affichage des progrès :
  - name: 📝 Install dependencies
      run: composer install --no-progress --no-suggest

#Exécution des tests PHPUnit
Enfin, nous exécutons les tests avec une couverture de code via PHPUnit :
 - name: 🔨 Run PHPUnit tests with coverage
      run: vendor/bin/phpunit --coverage-text

## Deuxième partie { COVERAGE } 

- name: Code Coverage Summary Report
      uses: irongut/CodeCoverageSummary@v1.3.0


