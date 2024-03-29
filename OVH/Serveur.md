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
$ useradd -m -s /bin/bash -d /home/$user $user #en root

$ su tadam 

$ mkdir ~/.ssh ~/log ~/www 

$ touch /.ssh/authorized_keys 

```
Copier les clefs ssh publiques souhaitées dans le fichier. (La clef à mettre et celle générée par mobaXterne lorsque tu load ta `private_key`)

Puis il faut générer une paire de clefs pour l'utilisateur (utilisation pour `Github` par exemple)
```
$ ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -q -P ""
```
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
`PM2` permet de déployer  sur le serveur des application `NodeJS`.

1- Installer `PM2`
```
$ sudo npm install pm2 -g
```
2- Pour lancer le serveur créer au préalable dans votre projet 
```
$ pm2 start <path/server.js> --name <nom>
```

Exemple de commandes : ATTENTION PM2 SE LANCE AVEC L'UTILISATEUR VOULU ET NON ROOT
```
# Redémarrer 
$ pm2 restart <id>

# Arrêter
$ pm2 stop <id>

# Supprimer 
$ pm2 delete <id>

# Lister les instances 
$ pm2 list

# Logs
$ pm2 log 
```
Il est possible d'activer le redéploiement automatique en cas de changement dans le dossier (automatisation avec git)
```
$ pm2 start <server.js> --watch
```

Il est aussi possible de créer un système de cluster aka de loadbalancing en cas d'erreur sur une instance grâce à la commande : 
```
$ pm2 start <server.js> -i <nb_clone_instance>
```

Tu peux aussi faire cette commande qui va générer automatiquement le fichier dont je parle juste en dessous. Puis une fois bien configuré la partie `apps` tu peux lancer la deuxième commande qui équivaut au `pm2 start server.js`.

```
$ pm2 ecosystem

$ pm2 start ecosystem.config.js
```
Possibilité d'accéder au dossier avec les logs d'erreur. `.pm2/logs/<log>`  

Une mise en production automatique avec git est possible grâce à la configuration d'un fichier `ecosystem.config.js` :
```

module.exports = {
  apps: [{
    name: 'vue-flow-form',
    script: './server.js',
    instances: 2,
    exec_mode: 'cluster',
    watch: true,
    env_production: {
      NODE_ENV: "production"
    }
  }],

  // Deployment Configuration
  deploy: {
    production: {
      key: '~/.ssh/id_rsa.pub',                            // Chemin vers la clef publique de l'utilisateur
      user: 'tadam',                                       // Utilisateur VM
      host: 'localhost',
      ref: 'origin/main',                                  // Branch
      repo: 'git@github.com:Mpreard/TadamFormulaire.git',  // Repository SSH
      path: '/home/tadam/www',                             // Path project on server
      'post-deploy': 'npm install && pm2 reload ecosystem.config.js'
    }
  }
};
```

### PM2 avec React 

``` 
# installe les dépendances du package.json
npm install

# construit le dossier de déploiement
npm run build

# lancer l'application
pm2 serve build <port utilisé dans le vhost> --spa 
```
---
## Installation de GIT
1- Installer git 
```
$ sudo apt install git
```
2- Aller sur le projet git et y rentrer la clef publique ssh de l'utilisateur   
3- Une config est dispo et visible avec
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

Fichier type de configuration `vhost` pour un projet sous serveur (Nodejs / React / ...) : 
``` bash
<VirtualHost *:80>
  ServerName <site sans www>
  ServerAlias <site avec www>

  DocumentRoot <path projet>

  ErrorLog /home/{user}/log/error.log
  CustomLog /home/{user}/log/access.log combined

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

Fichier type de configuration `vhost` pour un projet html / css :
```
<VirtualHost *:80>
  ServerName <site sans www>
  ServerAlias <site avec www>

  DocumentRoot <path projet>

  ErrorLog /home/{user}/log/error.log
  CustomLog /home/{user}/log/access.log combined

  <Directory /home/{user}/www/{user}/>
     Require all granted
  </Directory>
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
$ sudo certbot --apache --agree-tos --redirect --hsts --staple-ocsp --email <email> -d www.<site> -d <site>
```
5- Renouveler le certificat existant
```
$ sudo /letsencrypt/letsencrypt-auto renew

$ sudo service apache2 restart
```

### Commandes utiles : 
```
# liste les certificats existants
$ certbot certificates

# supprimer un certificat
$ certbot delete --cert-name <url>
```

En cas de nécessité à modifier un certificat il faut : 
```
$ a2dissite <site>-ssl.conf

$ systemctl reload apache2

# Choisir l'option d'Extend lors de cette commande
$ sudo certbot --apache --agree-tos --redirect --hsts --staple-ocsp --email <email> -d www.<site> -d <site>
```

---
## Ajouter un sous domaine 
Pour ajouter un sous domaine il faut dans un premier temps aller sur OVH `https://www.ovh.com/manager/web/#/domain/<domaine>/zone`. 

- Il faut cliquer sur `Ajouter une entrée`, et choisir le `A`. La config se fait toute seule après.   
- Il ne faut pas oublier de faire le `<sous-domaine>.<domaine>.<extension>` mais aussi le `www.<sous-domaine>.<domaine>.<extension>`  
- Prendre une invite de commande et ping les 2 domaines jusqu'à ce que les paquets soient reçu.   
- Il suffit de configurer son host apache2 (`Gestion des vhosts`).  

- En cas de changement de domaine d'un site déjà existant et sous certificat SSL : 
```
$ certbot -auto delete --cert-name <www.exemple.com>
```  
- Aller dans le fichier `<domaine>.conf` et retirer les 3 dernières lignes parlant de SEncrypt.   
- Supprimer le `vhost-le-ssl.conf`.
- Suivre la procédure `Certificat SSL`

---
## Gestion des logs avec Logrotate
Logrotate permet de compresser suivant un planning les logs afin qu'elles ne prennent pas trop de place.  
Pour l'installer taper en root :   
```
$ apt install logrotate

```
Il va s'installer dans /etc/  
Pour vérifier sa présence tape :
```
$ logrotate
```
Une fois ça fait il faut créer un fichier pour configurer la routine
```
$ touch /etc/logrotate.d/<nom-domaine>
```
Enfin rentrer ce code en adaptant à son utilisateur / site. (Une conf par site sera nécessaire)
```
/home/<user>/log/*.log {
  su  <user> <group (= user souvent)>
  daily
  missingok
  rotate 14
  compress
  delaycompress
  notifempty
  create 640 <user> <group>
  sharedscripts
  postrotate
          if /etc/init.d/apache2 status > /dev/null ; then \
          /etc/init.d/apache2 reload > /dev/null; \
          fi;
  endscript
  prerotate
          if [ -d /etc/logrotate.d/httpd-prerotate ]; then \
                  run-parts /etc/logrotate.d/httpd-prerotate; \
          fi; \
  endscript
}
```

---

## Vérifier l'état de son serveur
```bash
$ htop
```

## Mise à jour des paquets serveurs
```bash
$ apt update
$ apt list --upgradable
$ apt upgrade
```