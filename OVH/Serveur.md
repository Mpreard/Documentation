# Serveur

---
## Génération des clefs SSH
---
- Ouvrir MobaXterne
- Tools
- MobaKeyGen(SSH key generator)
- Type : `RSA`
- Number of bits : `4096`

---
## Sauvegarde et stockage des clefs SSH

- Save private dans `C:\Users\mpreard\.ssh` sous le format `<nom>.ppk`
- Save public dans `C:\Users\mpreard\.ssh` sous le format `<nom>.pub`
- Dans la fenêtre de génération -> `Conversersions` -> `Export SSH key` dans `C:\Users\mpreard\.ssh` sous le format `<nom>.pub`

---
## Insertion clef SSH OVH

- Aller sur `https://www.ovhcloud.com/fr/public-cloud/`
- Se connecter avec ton compte
- Aller dans le menu à droite -> `Clés SSH`
- `Ajouter une clef` -> Prendre la clef directement depuis la fenêtre de génération des clefs
- (Si cette fenêtre est fermée tu vas dans -> MobaKeyGen(SSH key generator) -> `Load` -> `Private_key.ppk`)

---
## Création serveur OVH

- Se connecter 
- Public Cloud
- Créer une instance 
- Suivre la procédure
- (Choisir debian 11)

---
## Ajouter / modifier utilisateur machine OVH

- Se connecter 
- Aller sur l'interface de la machine sur OVH
- Aller dans `contact & rights` 
- `Ajouter`
- Insérer l'identifiant OVH de l'utilisateur

---
## Connexion MobaXterne vers serveur

- Lancer MobaXterne
- `Session`
- `SSH`
- Remote host => IP publique du serveur (trouvable sur OVH dans `instances`)
- (Si première connexion à la machine username => `debian`)
- username => root
- `Advenced SSH settings`
- Choisir `Use private key` 
- Load ta private key

---
## Configuration de l'utilisateur root

``` bash
sudo su -
nano .ssh/authorized_keys
```
- Supprimer toutes les valeurs avant `ssh-rsa ...` => Permet la connexion en tant que root et non debian user

``` bash
nano /etc/ssh/sshd_config
PermitRootLogin prohibit-password  #décommenter cette ligne dans le fichier => Permet de se connecter en root à la machine
```
- Se deconnecter avec `exit`
- Clique droit sur la session
- Dubliquer la session existante
- Modifier la nouvelle session dupliquée 
- Mettre en username => `root`

---
## Gestion des utilisateurs
<u>Root</u> :   
- Mise à jour des paquets
- Création d'utilisateur
- Gestion des accès
- Installation des paquets

<u>User</u> :  
- Gestion du site par git  

<u>Commandes</u> :

- Lister les utilisateurs existants : 
``` bash
cat /etc/passwd
```
- Créer un utilisateur : 
``` bash
useradd -m -s /bin/bash -d /home/$user $user

mkdir .ssh

touch ~/.ssh/authorized_keys
```
Copier les clefs ssh publiques souhaitées dnas le fichier.  

- Supprimer un utilisateur : 
``` bash
userdel -r <nom>
```

---
## Gestion des accès par utilisateur (SSH)
- Se connecter avec l'utilisateur souhaité 
- Aller dans : 
```
cat .ssh/authorized_keys
```
- Ajouter la clef publique à la suite des précédentes (retour à la ligne)
- Chaque utilisateur possède son propre fichier `authorized_key` avec ses propres clefs SSH

---
## Installation NodeJs
``` bash
curl -fsSL https://deb.nodesource.com/setup_16.x | bash -

apt update

apt install nodejs
```

---
## Installation Composer
``` bash
curl -sS https://getcomposer.org/installer -o composer-setup.php

php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```

---
## Installation Apache2
``` bash
apt-get update && apt-get upgrade -y

apt-get install apache2
```

---
## Installation PM2
`PM2` permet de déployer sur le serveur des application `NodeJS`. 

1- Installer `PM2`
```
$ sudo npm install pm2 -g
```
2- Pour lancer le serveur créer au préalable dans votre projet 
```
$ pm2 start <path/server.js> --name <nom>
```

Exemple de commandes : 
```
# Redémarrer 
$ pm2 restart <id>

# Arrêter
$ pm2 stop <id>

# Supprimer 
$ pm2 delete <id>

# Lister les instances 
$ pm2 list
```

---
## Installation de GIT
1- Installer git 
```
$ sudo apt install git
```
2- Initialisation de l'utilisateur 
```
$ git config --global user.name "<name>"
$ git config --global user.email "<email>"
```  
3- Pour vérifier la configuration 
```
$ git config --list
``` 

---
## Gestion de l'utilisation des ports 
Permet de voir quel service utilise quel port : 
``` bash
netstat -laptn
```

---
## Créer un nouveau projet 
1- Créer un dossier dans 
```
$ mkdir /home/projects/<nomProjet>
``` 
2- Mettre son projet dedans  
3- Passer à la `Gestion des vhosts`

---
## Gestion des vhosts 
Avec Apache, un site web == un fichier de conf `vhost` dans `/etc/apache2/site-available`. La nomenclature est la suivante `<nomDomaine><extension>.conf` (exemple : maxime.fr.conf)  

Pour créer et activer un vhost, suivre les instructions suivantes :  

1- Il faut désactiver le vhost de base qui est `000-default.conf` avec la commande suivante 
```
$ sudo a2dissite 000-default.conf
```
2- Créer votre vhost sous la nomenclature montrée au dessus. Prendre le fichier type ci-dessous  

3- Il faut ensuite activer le vhost du projet grâce à 
```
$ sudo a2ensite <vhost.conf>
```
3- Test possible du fichier avec 
```
$ sudo apache2ctl configtest
```
4- Il faut maintenant reload apache
```
$ sudo systemctl restart apache2
```

En cas de modification d'un `vhost` il est nécessaire de lancer la commande à chaque fois : 
``` bash
systemctl reload apache2
```

Fichier type de configuration `vhost` : 
``` bash
<VirtualHost *:80>
  ServerName <site sans www>
  ServerAlias <site avec www>

  DocumentRoot <path projet>

  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined

  ProxyRequests Off
  ProxyPreserveHost On
  ProxyVia Full

  <Proxy *>
      Require all granted
  </Proxy>

  ProxyPass / http://127.0.0.1:<PortLibre>/
  ProxyPassReverse / http://127.0.0.1:<PortLibre>/
</VirtualHost>
```
---
## Certificat SSL (HTTPS)
Pour passer son site en sécurisé il est nécessaire de générer un certificat SSL. Pour cela `Certbot` est très utile.  

1- Donner les droits au dossier de votre projet avec la commande 
```
$ sudo chmod -R 755 /home/projects/<Projet>
```
2- Vérifier d'avoir bien créé le vhost du projet (cf Gestion des vhosts)    
3- Installer `python` + `certbot` : 
```
$ sudo apt install python3-certbot-apache -y
``` 
4- Adapter et lancer la commande suivante afin de créer le certificat SSL
```
sudo certbot --apache --agree-tos --redirect --hsts --staple-ocsp --email <email> -d www.<site>
```
