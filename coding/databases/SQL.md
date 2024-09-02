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
--clean ajoute des requetes comme DROP, ... pour s'assurer d'avoir un environnement propre
-d est optionnel, pg_dump s'attend directement au nom d'une base

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

PSQL commands:

- \q : quitter
- \l : lister les bdd
- \dt : liste des tables de la bdd courante
- \c database : se connecter à la database (+- use en SQL)
- \d table : describe table
- \d+ table : more detailed table description
- \timing : permet de chronométrer le temps d'exécution des commandes

collation : détermine l'ordre de tri

## Types

Type SERIAL dans Postgres: INT AUTO INCREMENT en SQL

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

INSERT INTO test VALUES (DEFAULT, 'Sport', 'Vendredi'); -- OK
INSERT INTO test VALUES (DEFAULT, 'Dormir', 'foo'); -- ERROR
```

### ARRAY

```sql
CREATE TABLE test (
	id SERIAL PRIMARY KEY,
	nom VARCHAR,
	couleurs VARCHAR [] -- that's an array
);
INSERT INTO test (nom, couleurs) VALUES ('Jean', ARRAY['bleu', 'red']);
SELECT couleurs[1] FROM test; -- indexes start at 1
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

- LIKE : sensible à la casse
	- WHERE name LIKE 'f%' : every name starting with f
	- WHERE name LIKE 'f_' : every name starting with f and followed by a single character
	- % : any
	- _ : single
- ILIKE : pas sensible à la casse
- IS NULL
- NOT
- IN

## Transtypage

- SELECT score, score::text FROM etudiants; -> Works
- SELECT prenom::int FROM etudiants; -> Error : text to number impossible
- SELECT score, cast(score as text) FROM etudiants; -> Works
- SELECT 'Score : ' || score FROM etudiants; -> Works: output a column of 'Score : 89' for example
- SELECT concat('Score : ', score) FROM etudiants; -> Works : same as above

## Dates

- Interrogations : prendre la date depuis le système:
	- SELEC now() FROM etudiants;;
	- SELECT CURRENT_DATE FROM etudiants;;
- formatage
	- SELECT to_char(now()::date, 'dd/mm/yyyy') FROM etudiants;
	- SELECT make_date(2020, 7, 14) FROM etudiants;
- extractions
	- SELECT extract(YEAR FROM ddn) AS year FROM etudiants;
	- SELECT extract(EPOCH FROM (now() - ddn)) AS sec;
	- WHERE extract(MONTH FROM ddn) = 9;
- calculs
	- SELECT now() - ddn FROM etudiants;
	- SELECT now()::date - ddn FROM etudiants;
	- SELECT CURRENT_DATE - ddn FROM etudiants;
	- AGE(ddn)
	- ddn + INTERVAL '20 years'
	- WHERE (date1, date2) OVERLAPS (date3, date4) : tell if those two periods overlaps

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
```

SELECT row_to_json(table) FROM table; -> Convertit chaque ligne en json
SELECT json_agg(json) FROM table; -> Mets tout un json dans une seule ligne

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
- Unions: Mix rows across tables
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

- CTE:


### Schema

If working with another schema than "public", we need to prefix all tables with the schema, or set it once:

```sql
SELECT * from my_schema.my_table;

-- Or

SET search_path TO my_schema;
SELECT * from my_table -- Ok 
```
## Optimization

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

## References

- Richard Carlier