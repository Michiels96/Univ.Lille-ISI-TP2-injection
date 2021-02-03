# Rendu "Injection"

## Binome

Michiels, Pierre, email: pierre.michiels.etu@univ-lille.fr
Saulquin, Clément/Aurélie, email: clement.saulquin.etu@univ-lille.fr


## Question 1

* Quel est ce mécanisme? 
Le mécanisme qui permet d'éviter à l'utilisateur de modifier la table est d'utiliser un script qui vérifie la structure de la chaine de caratère avec une regex.
* Est-il efficace? Pourquoi? 
Ce mécanisme est efficaces. En effet à partir du moment ou on veut exécuter une autre requête SQL à la place de celle prévue, on est obligé de séprarer la requête pirate avec un **;** qui sera automatiquement rejeter par la regex.

## Question 2

* Votre commande curl
Cette commande curl a été obtenue après avoir copier coller la commande curl généré par mon navigateur internte (brave).

```
curl 'http://127.0.0.1:8080/' -H 'Connection: keep-alive' -H 'Cache-Control: max-age=0' -H 'Upgrade-Insecure-Requests: 1' -H 'User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.67 Safari/537.36' -H 'Origin: http://127.0.0.1:8080' -H 'Content-Type: application/x-www-form-urlencoded' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9' -H 'Sec-Fetch-Site: same-origin' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-User: ?1' -H 'Sec-Fetch-Dest: document' -H 'Referer: http://127.0.0.1:8080/' -H 'Accept-Language: fr-FR,fr;q=0.9,en-US;q=0.8,en;q=0.7' --data-raw 'chaine=;DROP TABLE chaines;&submit=OK' --compressed
```

## Question 3

* Votre commande curl pour effacer la table

* Expliquez comment obtenir des informations sur une autre table

## Question 4

Rendre un fichier server_correct.py avec la correction de la faille de
sécurité. Expliquez comment vous avez corrigé la faille.

## Question 5

* Commande curl pour afficher une fenetre de dialog. 

* Commande curl pour lire les cookies

## Question 6

Rendre un fichier server_xss.py avec la correction de la
faille. Expliquez la demarche que vous avez suivi.


