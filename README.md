# TP: Etude d'un programme vulnérable en Python (Application Web)

Au cours de ce TP, vous allez étudier une application web minimaliste, qui est vulnérable à une faille d'injection SQL. 

Les prérequis sont:
 * Une machine sous Linux avec Python 3
 * Accès à un serveur MySQL (sur votre machine, ou ailleurs, peu importe)
 * Savoir programmer en Python
 * Connaitre les bases de l'HTML, du javascript, et du SQL
 * Avoir vu le cours sur les injections SQL

Vous pouvez utiliser votre machine OpenStack, comme dans le dernier sujet sur les permissions Unix.
Vous pouvez aussi utiliser l'image OVA disponible sur: https://nextcloud.univ-lille.fr/index.php/s/BMawwN5m8AcT9oJ

## Préparation

Dans ce dépôt, vouz trouverez le fichier `serveur.py` contenant le source python du serveur vulnérable.

### Préparation d'un environnement virtuel Python3

Un environnement virtuel Python3 est une sorte d'installation indépendante et auto-suffisante de Python3, qui sera placée dans votre dossier personnel, et dans laquelle vous pouvez installer des modules Python3 sans avoir besoin des droits root, ni de toucher au reste du système.

Si vous travaillez avec votre propre machine, assurez vous d'abord d'avoir installé l'outil de création d'environnement virtuel (sous Ubuntu/Debian: `sudo  apt install python3-venv`)

Ensuite, préparez l'environnement virtuel avec la commande suivante (où `<nom de dossier>` est à remplacer par le nom du dossier qui contiendra votre environnement virtuel)

```
python3 -m venv <nom de dossier>
```

Par exemple: `python3 -m venv monEnvTP`

### Activation de l'environnement virtuel

Pour travailler avec l'environnement virtuel que vous venez de préparer, vous devez d'abord l'activer avec la commande suivante:

```
source <chemin vers votre environnement virtuel>/bin/activate
```

Par exemple: `source monEnvTP/bin/activate`

Cette activation ne persiste que pendant la session shell/terminal en cours, et devra être refaite si vous vous deconnectez, ou relancez un terminal. Pour savoir si l'environnement virtuel est activé, vous pouvez vérifier que son nom apparait, entre parenthèses, dans votre invite de commande shell. 

A partir de maintenant, toute les étapes à réaliser dans ce TP (y compris la suite des étapes de préparation) nécessitent que l'environnement virtuel soit activé.

### Installation des modules Python3 nécessaires

Installez les modules nécessaires dans votre environnement (n'oubliez pas qu'il doit être activé) en tapant les commandes suivantes:

```
pip3 install cherrypy
pip3 install mysql-connector-python
```

Le module `cherrypy` est un framework d'applications Web en python, le module `mysql-connector-python` permet de se connecter à une base MySQL.

### Création de la table MySQL

Pour créer la table qui servira à l'application, connectez vous à votre base de données MySQL avec l'outil de votre choix, et créez la table suivante:

```
CREATE TABLE chaines (
	id int NOT NULL AUTO_INCREMENT, 
	txt varchar(255) not null, 
	who varchar(255) not null,
	PRIMARY KEY(id)
);
```

### Configuration de l'accès à la base de données

Renommez (ou copiez) le fichier de configuration de base de données `config.py.sample` en `config.py` et éditez le pour y mettre les informations d'accès à votre base MySQL.

Etant donné que le fichier `config.py` contient le mot de passe de votre serveur MySQL, vous prendez garde à ne pas le commit sur votre dépôt git (vous pouvez, par exemple, l'ajouter au `.gitignore`).

## Travail à réaliser

### Se familiariser avec l'application

Lancez l'application avec la commande suivante (en étant dans le dossier du dépôt git, et avec l'environnement virtuel activé):

```
./serveur.py
```

Les erreurs les plus fréquentes que vous pouvez rencontrer lors du lancement de du serveur sont:
 * Fichier `serveur.py` introuvable: vérifiez que vous êtes bien positionné dans le dossier du dépot git.
 * Modules introuvables (cherrypy ou mysql connector): assurez vous d'avoir activé l'environnement virtuel
 * Problème de connexion à la base de données: Vérifiez que les informations fournies dans `config.py` sont exactes
 * Port 8080 occupé: vérifiez que vous n'avez pas un autre serveur qui écoute sur le port 8080 (potentiellement une autre instance de `serveur.py` lancée sur un autre terminal)

Si cela fonctionne, vous aurez une ligne du type:
```
[03/Dec/2020:13:10:03] ENGINE Serving on http://127.0.0.1:8080
```

Vous devrez garder ce terminal ouvert pendant toute l'utilisation du serveur. Pour le stopper, faites un Ctrl-C dans le terminal où vous l'avez lancé.

Ensuite, allez sur `http://localhost:8080` avec votre navigateur, et testez le fonctionnement de la page. Notamment:
 * Testez l'ajout d'une donnée via le formulaire, et vérifiez qu'elle est rajoutée en base de donnée
 * Regardez le source de la page web
 * Regardez le source du programme serveur.py 

L'application permet d'inserer des chaines dans une table de la base de données (dans la colonne txt), tout en loggant l'adresse IP de la personne ayant envoyé cette chaine (dans la colonne who)

Vous pouvez regarder la documentation de CherryPy sur https://cherrypy.org/ et la documentation du connecteur MySQL python sur https://dev.mysql.com/doc/connector-python/en/

### Trouver une première vulnérabilité (injection SQL)

Rappel: la vulnérabilité d'injection SQL survient lorsqu'une requête SQL est construite de manière non sécurisée, par exemple en par concaténation de chaines, à partir d'élements fourni par un utilisateur potentiellement malveillant. 

Ainsi, si on imagine une requete construite comme ceci, alors que la variable `valeur` est controlée par l'utilisateur:
```
requete = "SELECT * FROM table WHERE champ='" + valeur "';"
```

Si la valeur donnée par l'utilisateur est: `'; <autre commande SQL>; --`, alors la variable requete
aura la valeur suivante: `SELECT * FROM table WHERE champ=''; <autre commande SQL>; --';` et l'autre commande SQL sera executée.

Sans rajouter de deuxieme commmande à executer, il est possible en général d'altérer la requete SQL pour pouvoir faire des comportements qui n'ont pas été prévus.

Ainsi, par exemple, si la valeur donnée par l'utilisateur est `' OR 1=1 --`, alors
la requete SQL executée sera: `SELECT * FROM table WHERE champ='' OR 1=1`, ce qui renverra toute les données de la table.

Examinez le source `serveur.py` pour trouver une vulnérabilité d'injection SQL. Pour vous aider, vous pouvez aussi afficher la requête MySQL en rajoutant un `print(requete)` dans la méthode `index`.

Examinez le source de la page web depuis votre navigateur. Un mécanisme a été mis en place pour tenter d'empecher l'exploitation de la vulnérabilité. 

#### Question 1 
Répondez aux questions suivantes:
 * Quel est ce mécanisme? 
 * Est-il efficace? Pourquoi? 

#### Question 2 : Contournement du mécanisme de validation

Avec l'outil `curl`, proposez une commande qui permet d'envoyer les données du formulaire au serveur sans passer par la validation. Vous pouvez vous aider des outils de développement de votre navigateur, qui permettent de créer automatiquement une ligne de commande `curl` à partir d'une requête http: https://ec.haxx.se/usingcurl/usingcurl-copyas

Essayez d'insérer dans la base de données des chaines qui comportent des caractères qui sont normalement interdits par la validation, et vérifiez que cela a bien été inséré dans la table.

#### Question 3 : Exploitation de la vulnérabilité 

En utilisant `curl`, et si besoin après avoir révisé le cours sur les injections SQL, réalisez une injection SQL qui insérer une chaine dans la base de données, tout en faisant en sorte que le champ `who` soit rempli avec ce que vous aurez décidé (et non pas votre adresse IP). Verifiez que cela a fonctionné ensuite.

L'exploitation d'injections SQL n'est pas limitée à la destruction de données. En supposant l'existence d'une autre table dans la base, imaginez un moyen d'utiliser cette faille d'injection SQL pour obtenir des informations sur les données de cette autre table (il n'est pas demandé de l'implémenter, mais d'expliquer une approche envisageable)

#### Question 4 : Corriger l'application vulnérable 

* Corrigez la faille de sécurité dans l'application. Vous pouvez lire https://pynative.com/python-mysql-execute-parameterized-query-using-prepared-statement/ pour avoir une idée de la marche à suivre.
* Essayez à nouveau l'exploitation de faille développée plus tôt, pour vous assurer que l'application n'est plus vulnérable. 

### Trouver une autre vulnérabilité (XSS)

Rappel: Une faille XSS survient quand un utilisateur potentiellement malveillant peut injecter des balises HTML dans une page, qui seront interprétées par le navigateur.

Ainsi, imaginons un service web qui renvoie une page dont le contenu est stocké dans cette variable chaine:
```
contenu_page = "<p> Bonjour, " + nom + "</p>"
```

Si une personne malveillante peut contrôler le contenu de la variable `nom`, alors elle peut y mettre des balises HTML, qui seront interprétées par le navigateur. Ceci constitue une faille de sécurité, car une de ces balises est la balise `<script>`, qui permet d'exécuter du code javascript sur le navigateur. Ce code javascript peut ensuite faire des actions malveillantes, tel que le vol de cookie de session...

Examinez le source `serveur.py` pour trouver une vulnérabilité XSS.

#### Question 5 : Exploitation de faille XSS

* D'abord, en utilisant `curl` réalisez une injection d'une balise script qui permet d'afficher une boite de dialogue sur l'écran de la personne qui visite le site. Pour info, la syntaxe en javascript est: `alert('Hello!')`

* Ensuite, en utilisant `curl` réalisez une exploitation de faille XSS qui permet de voler les cookies des visiteurs de la page. 

Pour ce faire, une méthode possible est de faire un code javascript qui modifie le `document.location`, pour rediriger la victime vers une
URL correspondant à un serveur que vous controllez, de manière a ce que le contenu de la requête contienne des informations utiles à voler.
Vous pouvez trouver les informations nécessaires ici:
https://developer.mozilla.org/fr/docs/Web/API/Document/location

Vous pouvez simuler un serveur pour récuperer les informations volées en utilisant la commande suivante:

```
nc -l -p <numero de port>
```

Ainsi, si quelqu'un va sur l'URL `http://<votre IP>:<numero de port>`, la requete complete sera affichée par `nc` (attention, cela ne fonctionne qu'une fois, il faudra relancer la commande nc si besoin est avant chaque nouvelle tentative)

#### Question 6 : Correction de la faille

Corrigez la faille XSS dans le fichier `server.py`, vous pouvez par exemple
utiliser la fonction `escape` du module python `html`. 

Ou vaut-il mieux réaliser ce traitement? Au moment de l'insertion des
données en base, au moment de l'affichage, les deux? Pourquoi ?

Essayez à nouveau l'exploitation de la faille développée plus tôt, pour
vérifier que l'application n'est plus vulnérable.
