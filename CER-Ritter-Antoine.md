# Prendre connaissance de la situation et la clarifier

## Contexte

Suite à une réunion avec les utilisateurs finaux, des contraintes ont été validés. Il faut passer à l’étape suivante en utilisant Hive. 

## Mots Clefs

- SGBD NoSQL 
- Hadoop
- Jointure
- Calcul d’agrégation
- Système de partitionnement 
- Base de données décisionnelle 

# Analyse du Besoin

## Problème(s)

- Comment utiliser Hive pour répondre à nos contraintes ? 
- Comment minimiser le temps d’exécution des requêtes ? 
- Comment mettre à jour les données de façon efficaces et régulière ? 
- Comment optimiser les performances de notre solution ? 

## Contraintes

- Performance du système
- Utilisation de Hive et Hadoop
- 3 jours

## Livrables

- Environnement Hadoop et Hive + intégration de la data dans la base de données
- Requêtes 
- Analyse des performances 

# Généralisation du problème

- Load and use (chargement et utilisation)

# Pistes de solutions

- Utiliser minimum 3 jointures ? 
- Hive est un logiciel de Data Warehouse 
- Notre modélisation impactera nos performances 
- Tous les 3 mois, nous devrons tout réimportés au lieu de juste réimporter la différence
- Le système de partitionnement allègera les requêtes 
- On ne peut pas rajouter d’informations à des données qui ont été anonymiser
- L’ETL pourra faire plus d’actions que simplement des calculs d’agrégations 
- Hive peut être distribué 
- NoSQL contient plusieurs bases de données (différentes sources)
- Le NoSQL apporte plus de possibilités que le SQL 
- Hive a des mécanismes de mise en cache pour accélérer les requêtes 
- La parallélisation de Hadoop peut être divisée en plusieurs étapes
- Il existe d’autres outils que MapReduce pour la parallélisation 

# Plan d’action

1. Se renseigner sur Hadoop 
2. Se renseigner sur le NoSQL 
3. Se renseigner sur Hive 
4. Se renseigner sur les Data Warehouse 
5. Créer schémas dans Hive 
6. Charger données
7. Faire les requêtes

# Notion de cours

## Mots clés

- **SGBD NoSQL** :
  Un Système de Gestion de Bases de Données (SGBD) NoSQL est conçu pour permettre la gestion de grandes quantités de données non structurées ou semi-structurées. Contrairement aux SGBD relationnels traditionnels, les SGBD NoSQL ne reposent pas sur un schéma fixe et permettent une plus grande flexibilité dans le stockage des données. Ils sont souvent utilisés dans des applications nécessitant une haute performance, une scalabilité horizontale et une faible latence.

- **Hadoop** :
  Hadoop est un framework open-source utilisé pour le stockage et le traitement de grandes quantités de données distribuées. Il est conçu pour s'exécuter sur des clusters de serveurs. Le cœur de Hadoop comprend deux composants principaux : le système de fichiers distribué Hadoop (HDFS), qui permet de stocker des données de manière distribuée, et MapReduce, un modèle de programmation pour le traitement et la génération de grands ensembles de données.

- **Jointure** :
  La jointure est une opération en base de données qui permet de combiner des enregistrements de deux ou plusieurs tables en utilisant une colonne commune entre elles. Les jointures sont couramment utilisées pour fusionner des tables sur la base d'une clé primaire et d'une clé étrangère, permettant ainsi de créer des relations entre les données de différentes tables.

- **Calcul d’agrégation** :
  Le calcul d'agrégation fait référence à l'utilisation de fonctions d'agrégation pour résumer ou combiner plusieurs valeurs en une seule. Les fonctions d'agrégation courantes incluent SUM (somme), AVG (moyenne), COUNT (compte), MIN (minimum) et MAX (maximum). Ces calculs sont souvent utilisés dans les requêtes SQL pour obtenir des statistiques sur les ensembles de données.

- **Système de partitionnement** :
  Un système de partitionnement divise une grande base de données ou un grand ensemble de données en partitions plus petites et plus gérables. Chaque partition peut être stockée, gérée et accédée indépendamment des autres. Le partitionnement peut améliorer les performances des requêtes et faciliter la gestion des données, notamment en permettant la distribution des partitions sur plusieurs serveurs.

- **Base de données décisionnelle** :
  Une base de données décisionnelle, ou entrepôt de données, est une base de données spécialement conçue pour le support à la décision et l'analyse de données. Elle centralise et stocke des données provenant de diverses sources opérationnelles afin de permettre une analyse historique, des rapports et des requêtes complexes. Les bases de données décisionnelles sont optimisées pour les requêtes en lecture et souvent structurées selon des modèles multidimensionnels, comme les cubes OLAP.

# Solutions

# Ressources

- [Building a Data Warehouse with Hive at Helpshift](https://medium.com/helpshift-engineering/building-a-data-warehouse-with-hive-at-helpshift-part-1-443046df6484)
- [Apache Hive Cookbook, Hanish Bansal, Shrey Mehrotra et Saurabh Chauhan, éditions Packt Publishing](https://univ.scholarvox.com/reader/docid/88843191/page/1)
- [HIVE- A data warehouse in HADOOP](https://www.analyticsvidhya.com/blog/2021/05/hive-a-data-warehouse-in-hadoop-framework/)
- [Apache Hive - site officiel](https://hive.apache.org/)
- [Apache Hive - définition](https://www.lebigdata.fr/apache-hive-definition)
- [Apache Hive Architecture](https://data-flair.training/blogs/apache-hive-architecture/)
- [Hive et Big data](https://meritis.fr/hive-et-big-data-exploration-des-concepts/)
- [Partitionnement](https://zephyrnet.com/fr/beginners-guide-for-data-partitioning-in-hiveql/)
- [Types de données dans Hive](https://cwiki.apache.org/confluence/display/hive/languagemanual+types)
- [NOSQL - généralités](https://www.ionos.fr/digitalguide/hebergement/aspects-techniques/nosql/)
- [Maitrisez les bases de données NOSQL](https://openclassrooms.com/fr/courses/4462426-maitrisez-les-bases-de-donnees-nosql)
- [Maitrisez les bases de données NOSQL - Le théorème de CAP](https://openclassrooms.com/fr/courses/4462426-maitrisez-les-bases-de-donnees-nosql/4462471-maitrisez-le-theoreme-de-cap)
- [ACID versus BASE](https://infodecisionnel.com/data-management/big-data/acid-versus-base/)
- [Blog TIME4BIGDATA](https://time4bigdata.wordpress.com/2015/03/23/hadoop-augmente-la-chaine-decisionnelle/)
- [Accélérer vos requêtes dans Apache Hive](https://www.adaltas.com/fr/2018/05/31/accelerating-query-processing-with-materialized-views-in-apache-hive-2/)
- [Using Apache Hive - Using materialized views](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_using_materialized_views.html)
- [Hive Language Manual - Joins](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Joins)
- [Vue Apache Ambari Hive](https://docs.microsoft.com/fr-fr/azure/hdinsight/hadoop/apache-hadoop-use-hive-ambari-view)