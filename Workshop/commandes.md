# Création du docker

## Volume

```sh
docker volume create hive
```

## Image

```sh
docker run -d -p 10000:10000 -p 10002:10002 --env SERVICE_NAME=hiveserver2 --mount source=warehouse,target=/opt/hive/data/warehouse --name hiveserver2 apache/hive:4.0.0
```

# Run

To run the connection:

```sh
docker exec -it hiveserver2 hive -u 'jdbc:hive2://localhost:10000/'
```

Test the connection:

```sh
set hive.cli.print.current.db=true;
```

Show all the databases:

```sh
show databases;
```

Create database:

```sh
CREATE DATABASE htables COMMENT 'Base de données du WS Hive';
```

```sh
ALTER DATABASE htables SET DBPROPERTIES ('owner'='A3', 'date'='2024-06-17');
```

Use the database:

```sh
USE htables;
```

Show the tables:

```sh
show tables;
```

# Next step

## Emploi_Population

```sh
CREATE EXTERNAL TABLE IF NOT EXISTS log_emploi_population (
  genre STRING,
  date_naissance DATE,
  fonction STRING,
  salaire STRING,
  code_postal STRING,
  id INT
)
COMMENT 'table intermediaire'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ';'
STORED AS TEXTFILE
LOCATION '/opt/hive/data/warehouse/emploi';
```

```sh
select * from log_emploi_population;
```

```sh
DESCRIBE FORMATTED log_emploi_population;
```

```sh
SHOW TBLPROPERTIES log_emploi_population;
```

```sh
CREATE TABLE IF NOT EXISTS emploi_population AS
SELECT
  SUBSTR(genre, 1, 1) AS sexe,
  ROUND(DATEDIFF(CURRENT_TIMESTAMP(), date_naissance) / 365.2425) AS age,
  fonction,
  salaire,
  code_postal,
  id AS Id_empl
FROM log_emploi_population;
```

## Personne

```sh
CREATE TABLE IF NOT EXISTS personne (
  id INT,
  firstname STRING,
  lastname STRING
)
COMMENT 'table interne'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ';'
STORED AS TEXTFILE
LOCATION '/opt/hive/data/warehouse/personne';
```

## Employés

```sh
CREATE TABLE IF NOT EXISTS employes (
  id INT,
  firstname STRING,
  lastname STRING,
  date_naissance STRING
)
COMMENT 'table interne'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ';'
STORED AS TEXTFILE
LOCATION '/opt/hive/data/warehouse/employes';
```

## Fonction Employés

```sh
CREATE TABLE IF NOT EXISTS fonction_employes (
  id_empl INT,
  annee_embauche STRING,
  Fonction STRING
)
COMMENT 'table interne'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ';'
STORED AS TEXTFILE
LOCATION '/opt/hive/data/warehouse/fonction';
```

## Salaire Employés

```sh
CREATE TABLE IF NOT EXISTS salaire_employes (
  sexe STRING,
  secteur STRING,
  salaire STRING,
  code_postal INT,
  id_empl INT
)
COMMENT 'table interne'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ';'
STORED AS TEXTFILE
LOCATION '/opt/hive/data/warehouse/salaire';
```

## Internautes Log

```sh
CREATE TABLE IF NOT EXISTS internautes_log (
  id_empl INT,
  annee_embauche STRING,
  Fonction STRING
)
COMMENT 'table interne'
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ';'
STORED AS TEXTFILE
LOCATION '/opt/hive/data/warehouse/internautes';
```

# Analyse Salaire

```sh
CREATE DATABASE analyse_salaires;
USE analyse_salaires;
```

# Partitions

```sh
set hive.exec.dynamic.partition=true;
set hive.exec.dynamic.partition.mode=nonstrict;
```

```sh
CREATE EXTERNAL TABLE population_genre (
  age INT,
  fonction STRING,
  salaire STRING,
  code_postal STRING,
  id INT
)
PARTITIONED BY (sexe STRING)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ',';
```

```sh
INSERT OVERWRITE TABLE population_genre
PARTITION (sexe)
SELECT age, fonction, salaire, code_postal, id_empl, sexe FROM emploi_population;
```

```sh
SHOW PARTITIONS population_genre;
```

```sh
SELECT * FROM population_genre WHERE sexe = 'F';
```

# Requêtes

## 1. Combien d’employés ont été embauchés après 2019 ?

```sh
SELECT count(*) FROM fonction_employes WHERE annee_embauche > 2019;
```

## 2. Quelles paires d’employés-secteurs ont un salaire supérieur à 35000 euro/année ?

```sh
SELECT distinct(secteur) FROM salaire_employes WHERE CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(salaire, 'euro', 1), 'an', 1) AS INTEGER) > 35000;
```

## 3. Lister les employés du secteur éducation recrutés après l’année 2010.

```sh
SELECT employes.nom, employes.prenom FROM employes INNER JOIN analyse_salaires.fonction_employes fe on employes.id_empl = fe.id_empl INNER JOIN analyse_salaires.salaire_employes se on employes.id_empl = se.id_empl WHERE fe.annee_embauche > 2010 AND se.secteur = 'education';
```

## 4. Quel employé a le plus d’expérience professionnelle ? 

```sh
SELECT employes.nom, employes.prenom
FROM employes
         INNER JOIN analyse_salaires.fonction_employes fe on employes.id_empl = fe.id_empl
WHERE fe.annee_embauche = (SELECT MAX(annee_embauche) FROM analyse_salaires.fonction_employes);
```

## 5. Quelle est le salaire moyen des employés ?

```sh
SELECT avg(CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(salaire, 'euro', 1), 'an', 1) AS INTEGER)) FROM salaire_employes;
```

## 6. Quelle est l’ensemble des fonctions des employées femmes par année d’embauche ?

```sh
SELECT annee_embauche, fonction
FROM fonction_employes
         INNER JOIN analyse_salaires.salaire_employes se on fonction_employes.id_empl = se.id_empl
WHERE se.sexe = 'F'
GROUP BY fonction, annee_embauche
SORT BY annee_embauche;

```

## 7. Lister les paires <prénom, fonction> des employés qui résident dans le département de paris ?

```sh
SELECT employes.prenom, fe.fonction
FROM employes
         INNER JOIN analyse_salaires.fonction_employes fe on employes.id_empl = fe.id_empl
         INNER JOIN analyse_salaires.salaire_employes se on employes.id_empl = se.id_empl
WHERE substr(CAST(se.code_postal AS STRING),1, 2) = '75';
```

## 8. Calculez le ratio des femmes et des hommes par rapport à la somme des salaires/par la somme des expériences professionnelles, et comparez les deux ratios.

```sh
SELECT
    (SELECT
        SUM(CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(salaire, 'euro', 1), 'an', 1) AS INTEGER)) /
        SUM(YEAR(CURRENT_DATE) - annee_embauche)
    FROM
        salaire_employes
    INNER JOIN
        analyse_salaires.fonction_employes fe ON salaire_employes.id_empl = fe.id_empl
    WHERE
        sexe = 'F') AS ratio_femmes,
    (SELECT
        SUM(CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(salaire, 'euro', 1), 'an', 1) AS INTEGER)) /
        SUM(YEAR(CURRENT_DATE) - annee_embauche)
    FROM
        salaire_employes
    INNER JOIN
        analyse_salaires.fonction_employes fe ON salaire_employes.id_empl = fe.id_empl
    WHERE
        sexe = 'M') AS ratio_hommes;
```

## 9.	Calculez le coefficient de corrélation avec la fonction corr() pour connaitre la relation de dépendance entre le nombre d’expérience et le salaires des employés.

```sh
SELECT
    CORR(
        CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(salaire, 'euro', 1), 'an', 1) AS INTEGER),
        YEAR(CURRENT_DATE) - annee_embauche
    ) AS correlation
FROM
    salaire_employes
INNER JOIN
    analyse_salaires.fonction_employes fe ON salaire_employes.id_empl = fe.id_empl;
```

## 10.	Le secteur d’activité influence-t-il le salaire des employés ? Conclure ? Indication : utiliser la solution de la question 10.

```sh
SELECT
    se.secteur,
    AVG(CAST(SUBSTRING_INDEX(SUBSTRING_INDEX(se.salaire, 'euro', 1), 'an', 1) AS INTEGER)) AS salaire_moyen
FROM
    salaire_employes se
INNER JOIN
    analyse_salaires.fonction_employes fe ON se.id_empl = fe.id_empl
GROUP BY
    se.secteur;
```
