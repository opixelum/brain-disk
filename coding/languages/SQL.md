[Professor's code](https://sharemycode.fr/sql)

Tools for database management:

- [DBeaver](https://dbeaver.io/)
- [DataGrip](https://www.jetbrains.com/datagrip/)
- [SchemaSpy](https://schemaspy.org/)
- [pgAdmin](https://www.pgadmin.org/)

For training: https://www.postgresqltutorial.com/
## Vocabulary

- Server
- User
	- Name
	- Password
	- Privilege (rights)
- Database
	- Schema
		- Table
			- Name : pluriel XOR singulier (cohérence dans toute la database), pas de chiffres, snake_case
			- Attribute (columns) : name, type, constraints
			- Key (primary, secondary)
- Query
	- Join
- Index
	- Speed up researches
	- storage
	- to slow writing
- Vues
	- Reusable queries
	- Alias of a query
	- Manage rights
- functions
	- predefined
	- triggers
	- procedures
- extensions
	- trusted
	- untrusted

Usage:
	- Direct: 27 %
	- ORM : 15 %
	- both : 54 %

- DDL : Data Definition Language
	- Create, Alter, Drop
- DML : Data Manipulation Language
	- insert, UPDATE, DELETE
	- DQL : Data Query Language
		- SELECT, SHOW
- TCL : Transaction Control Language
	- BEGIN, COMMIT, ROLLBACK
- DCL : Data Control Langage
	- GRANT, REVOKE

## Command Line

- psql : terminal
- pg_dump : sauvegarde d'une selection
- pg_dumpall : sauvegarde d'une bdd
- pg_restore : retauration
- createdb, dropdb
- pg_config
- pg_bench
- reindexdb, vacuumdb

Créer une base :

```sh
createdb -U user db
```

- Importer un script SQL

```sh
psql -U user -d db -f file.sql
```

- Entrer dans le terminal Postgres

```sh
psql -U user 
```

ou

```sh
psql -U user -d db
```

- Commande SQL depuis le terminal normal

```sh
psql -U user -d db -c "SELECT * FROM table;"
```

- Rediriger la sortie d'une commande SQL depuis le terminal normal

```sh
psql -U user -d db -c "SELECT * FROM table;" > table.txt
```

ou au format HTML

```sh
psql -U user -d db -P format=html -c "SELECT * FROM table;" > table.html
```

- Sauvergarder une bdd

```sh
pg_dump db > db.sql
```

ou au format binaire (fichier plus léger)

```sh
pg_dump -Fc db > db.dump
```

ou pour sauvegarder juste une table

```sh
pg_dump db -t table > table.dump
```

ou si nous ne sommes pas connecté au user postgres

```sh
pg_dump -U user -d db -t public.table --clean > table.sql
```

public n'est pas obligatoire (public est un schema)
`--clean` ajoute des requetes comme DROP, ... pour s'assurer d'avoir un environnement propre
`-d` est optionnel, pg_dump s'attend directement au nom d'une base

Sauvegarder avec des "INSTER TO" pour rendre le dump compatible avec tout SQL

```sh
pg_dump db --column-inserts > db.dump
```

Restaurer

```sh
pg_restore --list file.dump
```

Restaurer et convertir le fichier dump en SQL

```sh
pg_restore -f file.sql "file.dump"
```

Restaurer sans les données et convertir le fichier dump en SQL

```sh
pg_restore --schema-only -f file.sql "file.dump"
```

Restaurer avec uniquement les données et convertir le fichier dump en SQ

```sh
pg_restore --data-only -f file.sql "file.dump"
```

Dupliquer une db

```sh
pg_dump db > db.sql
createdb db_dup
psql -d db_dup < db.sql
```

Découvrir une bdd (pg_catalog est un schema contenant les infos sur tous les objets du système)

```sql
SELECT * FROM pg_catalog.pg_database;
SELECT * FROM pg_catalog.pg_namespace;
SELECT * FROM pg_catalog.pg_tables;
SELECT * FROM pg_catalog.pg_indexes;
SELECT * FROM pg_catalog.pg_class;
SELECT * FROM pg_catalog.pg_stats;
```

PSQL commands:

- `\q`: quitter
- `\l`: lister les bdd
- `\dt`: liste des tables de la bdd courante
- `\c <db>`: se connecter à la database (+- use en SQL)
- `\d <table>`: describe table
- `\d+ <table>`: more detailed table description
- `\timing`: permet de chronométrer le temps d'exécution des commandes

collation : détermine l'ordre de tri

## Types

Type `SERIAL` dans Postgres: `INT AUTO INCREMENT` en SQL

Bon typage:

- intégrité des données
- stockage optimisé
- tri fonctionnel (optimisation index)
- compréhension des données
- fonction
- respect des normes/standards

Utiliser les INT que si on doit faire des calculs avec. Un code postal est une chaine de caractères de 5 caractères, pas un nombre.

Pas de guillements (") en Postgres, uniquement des apostrophes (')

### ENUM 

```sql
DROP TYPE IF EXISTS jour_semaine CASCADE; -- Here, CASCADE will drop all columns of type jour_semaine in all tables
CREATE TYPE jour_semaine AS ENUM ('Lundi', 'Mardi', 'Mercredi', 'Jeudi', 'Vendredi', 'Samedi', 'Dimanche')

DROP TABLE IF EXISITS test CASCADE;
CREATE TABLE test (
	id SERIAL PRIMARY KEY,
	travail text,
	jour jour_semaine
)

INSERT INTO test VALUES (DEFAULT, 'Sport', 'Vendredi'); -- Ok
INSERT INTO test VALUES (DEFAULT, 'Dormir', 'foo'); -- ERROR: `foo` is not `jour_semaine`
```

### ARRAY

```sql
CREATE TABLE test (
	id SERIAL PRIMARY KEY,
	nom VARCHAR,
	couleurs VARCHAR [] -- That's an array
);
INSERT INTO test (nom, couleurs) VALUES ('Jean', ARRAY['bleu', 'red']);
SELECT couleurs[1] FROM test; -- Indexes start at 1
```

### Composite

- Similar to struct in programming languages

```sql
CREATE TYPE t_address AS (
	street VARCHAR(255),
	zip_code CHAR(5),
	city VARCHAR(255)
);

CREATE TABLE user (
	name VARCHAR(255),
	address t_address
);

INSERT INTO user (name, address) VALUES (
	'John',
	ROW ('123 Street', '75001', 'Paris')
);

SELECT name, address.street FROM user;
```

### SELECT

Ordre d'interpretation :
1. FROM ... : source des données (table)
2. WHERE ... : filtrage des lignes
3. SELECT ... : filtrage des colonnes
4. ORDER BY ... : ordre
5. LIMIT ... : nombre max de lignes dans la réponse

SELECT peut faire des pseudos colonnes

SELECT nom, prenom, score, score * 10 as mega_score
score * 10 (alias mega_score) sera une pseudo colonne qui contiendra le score multiplié par 10

Un alias peut etre utilisé sur ORDER BY  ou LIMIT, mais pas dans WHER à cause de l'ordre d'interprétation (WHERE intervient avant SELECT)

ORDER BY 1 : ordonne le resultat selon la 1ère colonne (l'index commence à 1, pas à 0 comme en programmation)

#### Opérateurs Logique

- `LIKE` : sensible à la casse
	- `WHERE name LIKE 'f%'`: every name starting with f
	- `WHERE name LIKE 'f_'`: every name starting with f and followed by a single character
	- `%`: any
	- `_`: single
- `ILIKE` : pas sensible à la casse
- `IS NULL`
- `NOT`
- `IN`

## Transtypage

```sql
SELECT score, score::text FROM etudiants; -- Ok 
SELECT prenom::int FROM etudiants; -- ERROR: text to number impossible
SELECT score, cast(score as text) FROM etudiants; -- Ok 
SELECT 'Score : ' || score FROM etudiants; -- Ok: outputs a column of 'Score : 89' for example
SELECT concat('Score : ', score) FROM etudiants; -- Ok: same as above
```

- Concatenation

```sql
CONCAT(first_name, ', ', last_name, ', ', phone_num) -- John, Doe, 0612345678
CONCAT_WS(',', first_name, last_name, phone_num) -- With separator: same as above
```

## Dates

- Interrogations : prendre la date depuis le système:

```sql
SELECt now() FROM etudiants;
SELECT CURRENT_DATE FROM etudiants;
```

- Formatage

```sql
SELECT to_char(now()::date, 'dd/mm/yyyy') FROM etudiants;
SELECT make_date(2020, 7, 14) FROM etudiants;
```

- Extractions

```sql	
SELECT extract(YEAR FROM ddn) AS year FROM etudiants;
SELECT extract(EPOCH FROM (now() - ddn)) AS sec;
WHERE extract(MONTH FROM ddn) = 9;
```

- Calculs

```sql
SELECT now() - ddn FROM etudiants;
SELECT now()::date - ddn FROM etudiants;
SELECT CURRENT_DATE - ddn FROM etudiants;
SELECT AGE(ddn) FROM etudiants;
SELECT ddn + INTERVAL '20 years' FROM etudiants
WHERE (date1, date2) OVERLAPS (date3, date4); -- Tells if those two periods overlaps
```

### JSON

```sql
INSERT INTO table (nom, adresse)
VALUES (
    'Jean',
    '{
		"ville": "Paris",
	    "cp": 75013,
        "appartment": {
	        "surface": 32
	    }
    }'
)
SELECT adresse->>'ville' as ville FROM table;
SELECT adresse->'appartment'->>'surface' FROM table
WHERE adress->>'ville' = 'Paris';

SELECT row_to_json(table) FROM table; -- Convertit chaque ligne en json
SELECT json_agg(json) FROM table; -- Mets tout un json dans une seule ligne
```

- JSONb: binary JSON
- Hstore: similar

Export:

- To CSV:

```sql
COPY table TO 'path/to/file.csv' DELIMITER ',' CSV HEADER;
```

- To JSON

```sql
COPY (
	SELECT json_agg(json) FROM table
) TO 'path/to/file.json';
```

[Cheat sheet for JSON in Postgres](https://devhints.io/postgresql-json)

### Constraints

- `PRIMARY KEY`: Column is primary key
- `NOT NULL`: Column cannot contain any null values
- `DEFAULT <value>`: When inserting without specifying any value, the default one is used
- `CHECK (<condition>)`: Allow insertions that are true to the condition
- `CONSTRAINT`: Constraint used for the whole table

```sql
CREATE TABLE my_table (
	id SERIAL PRIMARY KEY,
	name VARCHAR(255) NOT NULL,
	created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
	age INT CHECK (age >= 0), -- Unsigned in MySQL
	percentage INT CHECK (percentage >= 0 AND percentage <= 100),
	CONSTRAINT check_table CHECK (percentage <= 10 * age)
);

INSERT INTO my_table VALUES (1, 'Jane', default); -- Good
INSERT INTO my_table VALUES (2, 'John', null); -- Wrong
INSERT INTO my_table (name) VALUES ('John'); -- Good
INSERT INTO my_table (name, age, percentage) VALUES ('Jack', 20, 99); -- Good
INSERT INTO my_table (name, age, percentage) VALUES ('James', -1, -10); -- Wrong
INSERT INTO my_table (name, age, percentage) VALUES ('James', 5, 51); -- Wrong
```

### Foreign Keys

```sql
CREATE TABLE country (
	id SERIAL PRIMARY KEY,
	name VARCHAR(255)
);

CREATE TABLE capital (
	id SERIAL PRIMARY KEY,
	name VARCHAR(255),
	id_country INT,
	CONSTRAINTS fk_country
		FOREIGN KEY (id_pays)
		REFERENCES country(id)
		    ON DELETE CASCADE -- or SET NULL
)
```

If we don't add `ON DELETE`, no country can be deleted.

### Generated Columns

```sql
CREATE TABLE my_table (
	id SERIAL PRIMARY KEY,
	price NUMERIC,
	tax NUMERIC,
	total_price NUMERIC GENERATED ALWAYS AS (price + tax) STORED
)
```

### Champs calculés

```sql
ALTER TABLE fournisseurs
ADD COLUMN adresse1 TEXT GENERATED ALWAYS AS (
	adr_num || ' ' || adr_typevoie || ' ' || adr_libvoie
) STORED;
```

### Aggregates

Apply functions on a group of results

```sql
SELECT DISTINCT age from students; -- Gives unique values of age column
SELECT COUNT(*) from students; -- Gives the number of students
SELECT SUM(work_hours) from students; -- Gives the total number of work hours 
-- Same for AVG(), MIN(), MAX(), ...

SELECT name, grade FROM students
GROUP BY grade
ORDER BY grade;

SELECT name, grade FROM students
GROUP BY ROLLUP (grade) -- Count the number of rows with each grade
ORDER BY grade;

SELECT name, grade FROM students
GROUP BY CUBE (grade, age) -- Count the number of any combination
ORDER BY grade;

SELECT name, STRING_AGG(city, ',') FROM students GROUP BY age; -- Gives the cities where each student of each age lives in

SELECT name, STRING_AGG(DISTINCT city, ',') FROM students GROUP BY age; -- Same but with unique cities in the result

SELECT name, JSON_AGG(city) FROM students GROUP BY age; -- Displays cities in JSON format
```

### Window Function

```sql
SELECT id, commune, hauteur, etat,
	ROW_NUMBER() OVER (ORDER BY etat) AS rang
FROM arbres
ORDER BY commune;

SELECT id, hauteur,
    SUM(hauteur) OVER (ORDER BY id) AS cumul
FROM arbres;

-- id | hauteur | cumul
-- ---+---------+------
-- 1  | 22      | 22
-- 2  | 11      | 33
-- 3  | 33      | 66
```

### Multi-Tables

- Joins: Mix columns across tables
- Unions: Mix rows across tables:

```sql
-- Shows common values from both tables
SELECT name FROM table1 UNION SELECT name FROM table2; -- Shows unique values
SELECT name FROM table1 UNION ALL SELECT name FROM table2; -- Shows all values

SELECT 'table1' AS source, name FROM table1
UNION
SELECT 'table1' AS source, name FROM table2;
-- source | name
-- -------+-----
-- table1 | John
-- table2 | Jane

-- Shows not common values from both tables
SELECT name FROM table1 EXCEPT SELECT name FROM table2;

-- Shows only common values from both tables
SELECT name FROM table1 INTERSECT SELECT name FROM table2;
```

- Sub-queries:

```sql
-- Quels sont les employés qui gagnent plus que la moyenne des salaires ?
SELECT nom, salaire
FROM employes
WHERE salaire > (SELECT AVG(salaire) FROM employes)
ORDER BY salaire DESC;

-- If we want to display the average:
SELECT nom, salaire, (SELECT AVG(salaire) FROM employes) as moy
FROM employes
WHERE salaire > (SELECT AVG(salaire) FROM employes)
ORDER BY salaire DESC;

-- Quels employés habitent le même département que les 2 + gros salaires ?
SELECT nom, salaire, adr_departement FROM emps
WHERE adr_departement IN (
    SELECT adr_departement FROM emps ORDER BY salaire DESC LIMIT 2
) ORDER BY salaire DESC;

-- Table dérivée
SELECT e.nom, e.salaire, moyenne_salaire.salaire_moyen
FROM (
	SELECT AVG(salaire) AS salaire_moyen
	FROM employes
) AS moyenne_salaire, employes e
WHERE e.salaire > moyenne_salaire.salaire_moyen
```

### Common Table Expression (CTE)

- Temporary view during a query
- Better code readability
- Good performances
- Recursivity : Useful for finding the full hiearchy

```sql
WITH moyenne AS (
	SELECT AVG(salaire) AS moyenne
	FROM emps
)
SELECT nom, salaire, m.moyenne
FROM emps
	CROSS JOIN moyennes AS m
WHERE salaire > M.moyenne
ORDER BY salaire DESC;

-- Recursive CTE
WITH RECURSIVE cte_name (column1, column2, ...)
AS(
    -- anchor member
    SELECT select_list FROM table1 WHERE condition

    UNION [ALL]

    -- recursive term
    SELECT select_list FROM cte_name WHERE recursive_condition
) 
SELECT * FROM cte_name;

-- Example
WITH RECURSIVE employee_hierarchy AS (
    SELECT
        id,
        nom,
        id_manager,
        CAST(NULL AS VARCHAR) AS nom_chef,
        1 AS niveau,
        CAST(id AS VARCHAR) AS path -- le chemin !
    FROM school.entreprise
    WHERE id_manager IS NULL

    UNION ALL

    SELECT
        e.id,
        e.nom,
        e.id_manager,
        eh.nom AS nom_chef,
        eh.niveau + 1 AS niveau,
        eh.path || '<-' || e.id AS path -- le chemin !
    FROM school.entreprise e
    JOIN employee_hierarchy eh ON e.id_manager = eh.id
)
SELECT
    id,
    REPEAT(' ', (niveau - 1) * 4) || nom AS nom,
    path
FROM employee_hierarchy
ORDER BY path;
```

### Joins

- Multiple tables
- Types: inner, outer, left, right, cross, intersections
- Common column: FK -> PK

```sql
-- Left table is the primary table. We will find the references in the right one
-- We will have all `cours`, but not all `salles`
SELECT cours.nom AS cours, salles.libelle AS salle
FROM cours LEFT JOIN salles
	ON cours.id_salles = salles.id_salles;

-- Right table is the primary table. We will find the references in the left one
-- We will have all `salles`, but not all `cours`
SELECT cours.nom, salles.libelle
FROM cours RIGHT JOIN salles
	ON cours.id_salles = salles.id_salles;

-- We will have all `salles` and all `cours`
SELECT cours.nom, salles.libelle
FROM cours FULL JOIN salles
	ON cours.id_salles = salles.id_salles;

-- We will have `salles` and `cours` that have references.
SELECT cours.nom, salles.libelle
FROM cours INNER JOIN salles
	ON cours.id_salles = salles.id_salles;

-- Cartesian product (all combinations possible)
-- It combines each row from the first table with each row from the second table
SELECT cours.nom, salles.libelle
FROM cours CROSS JOIN salles
	ON cours.id_salles = salles.id_salles;

-- Self-join / Recursive join
SELECT
	employes_table.nom AS employes,
	managers_table.nom AS managers
FROM
	entreprise AS employes_table
	LEFT JOIN entreprise AS managers_table on employe.id_manager = chef.id;

-- JOIN with 3 tables
SELECT
	cours.nom AS cours,
	salles.libelle AS salle,
	batiments.libelle AS batiment
FROM
	cours AS c
	LEFT JOIN salles AS s USING (id_salles)
	LEFT JOIN batiments AS b USING (id_batiments);
	-- USING can be used if PK and FK have the same name
```

### Temporary Tables

```sql
-- Temporary table lasts until the server is shutdown
CREATE TEMORARY TABLE temp_emps AS
	SELECT *
	FROM emps
	ORDER BY RANDOM()
	LIMIT 5;
```

### Views

Raccourci vers une requête.

```sql
CREATE VIEW v_emps AS
	SELECT *
	FROM emps
	ORDER BY RANDOM()
	LIMIT 5;
```
### Schema

If working with another schema than "public", we need to prefix all tables with the schema, or set it once:

```sql
SELECT * from my_schema.my_table;
-- Or
SET search_path TO my_schema;
SELECT * from my_table;
```

## Geometry

- Types: points, polygons, ...
- Geometric functions
- SIG functions (QGIS)
	- Earth curve
	- SRID: Spatial Reference System Identifier
	- Web Mercator 4326 = km
	- 2857 = degrés
- ST = Spatial Temporal

```sql
CREATE EXTENSION postgis;

ALTER TABLE my_table
	ADD COLUMN geometry geometry(POINT, 4326);

-- latitude longitude
UPDATE my_table
	SET geometry = ST_SetSRID(ST_MakePoint(longitude, latitude), 4326);

CREATE INDEX idx_villes_geometry ON villes USING GIST (geometry);

SELECT geometry, ST_AsText(geometry) from villes;

-- Distance between two points
SELECT ST_DistanceSphere(
	ST_MakePoint(-2.76924, 47.52613), -- Sarzeau
	ST_MakePoint(-2.82263, 47.62630)  -- Arradon
) / 1000 AS distance_km;

SELECT
	id, nom,
	ST_AsText(ST_Centroid(geometry)) AS center,
	ST_Area(geometry) AS surface,
	ST_Perimeter(geometry) AS perimeter
FROM public.departements;

-- Inside a geometry
SELECT v.id, v.nom, v.geometry
FROM public.villes v
JOIN public.departements d ON ST_Within(v.geometry, d.geometry)

-- Geometry that touches others
SELECT d.id, d.nom
FROM public.departements d
WHERE ST_Touches(d.geometry, (SELECT geometry FROM public.departements))
```

### Full Text

- tsvector: text search vector (storage): strings converted in numbers
- tsquery (queries)
- index GIN
- Colonne calculée : maintenir à jour le vecteur de texte
- Donner du poids au mots (tel mots est plus important que tel mots, ...)

Without full text :

```sql
-- Liste des maisons dont la description parle de Zola
-- Not robust: the word 'Gorgonzola' can be found, which is not what we want
SELECT illustre, nom, nom_usuel 
FROM maisons
WHERE description ILIKE '%zola%';

-- Liste des maisons qui parlent de Victor Hugo dans les 4 champs
SELECT illustre, nom, nom_usuel
FROM maisons
WHERE (illustre    ILIKE '%victor%' AND illustre    ILIKE '%hugo%')
   OR (nom         ILIKE '%victor%' AND nom         ILIKE '%hugo%')
   OR (nom_usuel   ILIKE '%victor%' AND nom_usuel   ILIKE '%hugo%')
   OR (description ILIKE '%victor%' AND description ILIKE '%hugo%');
```

With full text:

```sql
-- Weight A is the most important, Z the least one
ALTER TABLE maisons
	ADD COLUMN search_vector TSVECTOR GENERATED ALWAYS AS (
		setweight(to_tsvector('french', coalesce(illustre, '')),    'A') ||
		setweight(to_tsvector('french', coalesce(nom, '')),         'B') ||
		setweight(to_tsvector('french', coalesce(nom_usuel, '')),   'B') ||
		setweight(to_tsvector('french', coalesce(description, '')), 'C')
	) STORED;

CREATE INDEX idx_maisons_search ON maisons USING GIN(search_vector);

-- Better search of Zola
-- No 'Gorgonzola'
SELECT illustre, nom, nom_usuel 
FROM maisons
WHERE search_vector @@ to_tsquery('french', 'Zola');

-- Gives weight to results
SELECT illustre, nom, nom_usuel,
	ts_rank(search_vector, to_tsquery('french', 'Zola')) AS rank
FROM maisons
WHERE search_vector @@ to_tsquery('french', 'Zola')
ORDER BY rank DESC;

-- Set words used for search in bold, and show the text around (headline)
-- plainto_tsquery : gives same results for 'Victor Hugo' and 'Hugo Victor'
SELECT illustre, nom, nom_usuel,
	ts_headline('french'), description, plainto_tsquery('french', 'Victor Hugo')) AS extrait
FROM maisons
WHERE search_vector @@ plainto_tsquery('french', 'Victor Hugo')
```

## Optimization

### Performances

1. Parsing = syntaxe
2. Rewriting = réécriture (vues, fonctions, ...)
3. planning =
	- générer des plans d'exécution (quel méchanisme utiliser pour éxécuter une requête)
	- évaluer le coût de chaque (compare le cout computationnel de chaque instructions, pas le temps)
	- sélectionner le meilleur
4. lock aquisition = récupération des verrous (en cas de multi utilisateur, sécurise les tables utilisées pour la requête)
5. execution de la requête
6. results = envoyer les résultats
7. commit / rollback = commit si pas d'erreur, rollback sinon (uniquement pour des requêtes de modification (INSERT, ALTER, ...))
8. relacher les verrous

### Les différents coûts

- Coût du CPU : Calculs fonctions, WHERE, JOIN, ORDER, AGG
- Coût du I/O (lecture/écriture disque) : SELECT, INSERT INTO, ALTER, INDEX
- Taille des données : nombre de lignes/colonnes

### Préparer l'analyse

- Préparer l'analyse en mettant à jour les statistiques
- Nettoyer, réorganiser (défragmentation, ...)

```sql
ANALYSE my_table; -- Update stats of a table
ANALYSE; -- Update the whole database

-- Clean & reorganize database
VACUUM; -- 118,705 ms
VACUUM ANALYSE; -- 483,072 ms
VACUUM FULL ANALYZE; -- 2739,214 ms
-- `FULL` locks all tables during the process
```

### Analyse

- Add `EXPLAIN` before your query to obtain the query plan.
- Query plan: technical stack trace of all executions for the query

- `EXPLAIN (ANALYSE)`: éxecution réelle
- `EXPLAIN (COSTS)`: affiche les couts estimés
- `EXPLAIN (VERBOSE)`: détails supplémentaires
- `EXPLAIN (BUFFERS)`: détails sur la mémoire
- `EXPLAIN (FORMAT JSON)`: exporte détails sous format json

```sql
EXPLAIN
SELECT ...

-- It can take parameters
EXPLAIN (ANALYSE, COSTS)
...
```

### Réécriture de Requêtes

- [Pev2](https://github.com/dalibo/pev2)

Instructions gourmandes:
- ORDER BY
- DISTINCT,
- GROUP BY

- L'ordre des éléments d'une jointure peut avoir un impact sur le temps d'exécution
- Une vue peut cacher une requête non-optimisée

### Index

- INDEX: B-Tree index for basic data (INT, NUMBER, ...)
- GiST: For complex data (geographic, spacial, ...)
- GIN: For sets (ARRAY, JSONB)
- SP-GiST: for trees
- BRIN: for big tables (logs)

```sql
CREATE INDEX idx_nom ON villes (nom); -- Speeds up queries on the `nom` column, especially for equals condition (WHERE nom = 'Paris'), not for ILIKE for example

CREATE INDEX my_index ON my_table USING GIST (column1, ...);
```

## PL / pgSQL

- Postgres programming language
- Inspired by PL/SQL (Oracle programming language)
- SQL is a non-procedural language (no variables, no control structs)
- PL: Procedural Language
- Language assez ancien, proche du Pascal et de l'ADA
- Pourquoi PL/pgSQL au lieu d'un autre language (genre Python) ? Pour centraliser le code
- Performances : plus rapide car s'éxécute localement, directement dans le moteur de la bdd
- Très simple

- Anonymous Code Block Execution (DO)
- Procédure stockée
	- Pas de valeur de retour
	- Contrôle transactionnel : COMMIT / ROLLBACK
- Fonction stockée
	- Retourne une valeur
	- Utilisable dans un `SELECT`

```sql
DO $$
DECLARE
	my_variable INTEGER := 10;
	counter INTEGER := 0;
	maxi INTEGER := 3;
BEGIN
	my_variable := my_variable * 5.55
	RAISE NOTICE 'Valeur : %', my_variable; -- Équivalent de `print`
	-- Output
	-- Valeur : 56 (arrondi à l'unité près car c'est un integer)

	-- Structure conditionnelle : IF, ELSIF, ELSE, END IF
	IF my_variable < 10 THEN
		RAISE NOTICE 'La valeur est inférieure à 10';
	END IF;

	-- boucle : LOOP, WHILE, FOR
	LOOP
		counter := counter + 1;
		RAISE NOTICE 'LOOP: counter = %', counter;
		EXIT WHEN counter >= maxi;
	END LOOP;

	counter := 0;
	WHILE counter < maxi LOOP
		counter := counter + 1;
		RAISE NOTICE 'WHILE: counter = %', counter;
	END LOOP;

	FOR i IN 1..maxi LOOP
		RAISE NOTICE 'FOR: i = %', i;
	END LOOP;
END $$;

```

### Cursor

```sql
```


### Fonctions

- Paramètres
- Type de retour
- Traitement qui se termine par `RETURN`
- Précision d'un langage

```sql
CREATE OR REPLACE FUNCTION my_add(a NUMERIC, b NUMERIC)
	RETURNS NUMERIC
AS $$
DECLARE
	total NUMERIC;
BEGIN
	total := a + b;
	RETURN total;
END $$ LANGUAGE plpgsql

-- In SQL
SELECT hauteur, envergure, my_add(hauteur, envergure) FROM arbres;

CREATE OR REPLACE FUNCTION arbres_info(mini NUMERIC)
	RETURNS SETOF arbres -- La structure de la table arbres
AS $$
BEGIN
	RETURN QUERY
		SELECT *
		FROM arbres
		WHERE envergure > mini;
END $$ LANGUAGE plpgsql;
```

## Procedure

- Fonction sans valeur de retour

```sql
DROP PROCEDURE IF EXISTS my_proc;

CREATE OR REPLACE PROCEDURE my_proc(tree_id INTEGER, new_height NUMERIC)
LANGUAGE plpgsql
AS $$
BEGIN
	UPDATE arbres SET hauteur = new_height WHERE id = tree_id;
END $$;

CALL my_proc(1, 123);
```

### Trigger

- Action sur une table qui déclenche une fonction
- Utile par exemple quand nous avons une colonne "updated_at", qui est mise à jour par un trigger à chaque update d'une ligne.
- Action:
	- BEFORE UPDATE
	- AFTER UPDATE
	- BEFORE INSERT
	- AFTER INSERT
	- ...
- Une table:
	- ON my_table

```sql
CREATE OR REPLACE FUNCTION trigger_set_update_at()
RETURNS TRIGGER AS $$
BEGIN
	-- We can also save the previous state in another table,
	-- then update the current one
	NEW.updated_at = NOW()
	RETURN NEW;
END $$ LANGUAGE plpgsql;

CREATE TRIGGER trig_clients_update
BEFORE
	UPDATE
ON clients
FOR EACH ROW -- Each row concerned by the query
	EXECUTE FUNCTION
		trigger_set_update_at();

-- Save table in history after delete
CREATE OR REPLACE FUNCTION trigger_historisation()
RETURNS TRIGGER AS $$
BEGIN
	INSERT INTO historisation (data) VALUES (row_to_json(OLD));
	RETURN OLD;
END $$ LANGUAGE plpgsql;

CREATE TRIGGER trig_historisation
AFTER DELETE ON cients
FOR EACH ROW
EXECUTE FUNCTION trigger_historisation();
```

## Read more

- [PostgreSQL Anonymizer](https://postgresql-anonymizer.readthedocs.io/)
- [PostgreSQL Faker](https://gitlab.com/dalibo/postgresql_faker)

## References

- Richard Carlier
