# Brief 2 : Déploiement et configuration des VMs
<div id="top"> 
      
### **Sommaire**

- [**Création des VMs**](#CréaVM)  
    - [Azure : exemple de la VM Administrative](#AzureAdmin)
- [**VM Administration**](#VMAdmin)
    - [Configuration des accès SSH](#ConfigSSH)
    - [Choix du numéro du port TCP](#PortTCP)
- [**VM Base De Données**](#VMBDD)
  - [Installation de MariaDB](#MariaDB)
  - [Configuration du nouveau disque](#NouvDisque)
- [**VM Application**](#VMAppli)
    - [Affectation d'un Nom de Domaine sur la plateforme Azure](#NomDomaine)
    - [Installation Apache Server](#ApacheServer)
    - [Choix du numéro du port TCP](#PortTcp)
      - [Sur Apache](#ApachePort)
      - [Sur Azure](#AzurePort)
  - [Installation de PHP et Nextcloud](#NextcloudApp) 
  - [Configuration du chemin entre Mariadb et Nextcloud](#ConfigChemin)
- [**Nextcloud**](#nextcloud)
    - [Création du compte administrateur de Nextcloud](#connexionNC)
    - [Interface utilisateur](#nextcloud_user)
    - [Ajout de fichiers](#AjoutFichier)

- [**Bonus**](#bonus)
    - [Proxyjump](#bonusproxy)
    - [Certification SSL](#certbot)
        - [Installation Certbot](#cerbot_install)

<div id=CréaVM>
    
## **Création des VMs**

<div id=AzureAdmin> 
    
### Azure : exemple de création pour la VM Administration

![](https://i.imgur.com/9pXcTXh.png)
![](https://i.imgur.com/KXPcB1O.png)
![](https://i.imgur.com/Ggg5eQz.png)
![](https://i.imgur.com/MmuYE2O.png)
![](https://i.imgur.com/ZbFUBrk.png)
![](https://i.imgur.com/DUBbhza.png)
![](https://i.imgur.com/vpQXH5R.png)
![](https://i.imgur.com/53P8OCa.png)
![](https://i.imgur.com/35B9CDD.png)
![](https://i.imgur.com/r3MfWPd.png)
![](https://i.imgur.com/016Cn3l.png)
![](https://i.imgur.com/Ku3hzds.png)
 

    
<div id=VMAdmin>
    
## **Configuration de la VM Administration**
    
<div id=ConfigSSH> 
    
### Configuration des accès par SSH avce des clées RSA

Création d'une clée publique RSA de la VM Administration pour se connecter et administrer en SSH le serveur Application et le serveur BDD.

```console
    ssh-keygen -b 4096 -t rsa

```
Ajout des clées RSA des ordinateurs administrateurs autorisés dans le fichier de gestion des autorisations de connection SSH à la machine bastion. 
    
```console
    nano .ssh/authorized_keys 

```
![](https://i.imgur.com/jkWtZRt.png)

    
    
[&#x23F6;](#top)

<div id=PortTCP>
    
### Choix du numéro du port TCP

Ouverture du fichier de configuration gérant les ports SSH :
```console
sudo nano /etc/ssh/sshd_config
```

Changement du port **22** par le port **10022** :
![](https://i.imgur.com/s7F9gDb.png)  

Redémarrage du service gérant les ports SSH pour prendre en charge le changement :
```console
sudo systemctl restart sshd
```

Vérification que le port 10022 est bien ouvert :
```console
ss -plnt
```
![](https://i.imgur.com/BeskfNq.png)

Un fois le port 10022 ouvert, on peut l'ajouter et supprimer le port 22 dans Azure, pour la VM Admin, dans l'onglet *Mise en réseau*.
 
![](https://i.imgur.com/s5FmBnC.png)
   
    
Pour se connecter à la VM Admin sur le port 10022 :
```console
ssh usergroupe@ippublic -p "num de port"
```

![](https://i.imgur.com/LPj0zDx.png)
    
[&#x23F6;](#top)    
    
<div id=VMBDD>
    
## **Installation et configuration de la VM Base de données**
        
Connexion en SSH depuis la VM Administration vers la VM Base de donnée pour installer MariaDB
        
![](https://i.imgur.com/elBxZRE.png)
     
    
    
[&#x23F6;](#top)
    

<div id=MariaDB>
        
### Installation de MariaDB
    
```console

sudo apt -y update

```

```console

sudo apt -y install curl software-properties-common gnupg2

```
    
```console

sudo apt -y upgrade

```
    
```console
sudo reboot
```
    
```console
curl -LsS -O https://downloads.mariadb.com/MariaDB/mariadb_repo_setup
sudo bash mariadb_repo_setup --mariadb-server-version=10.8.3
```
    
```console
sudo apt install mariadb-server mariadb-client
```
    
```console
sudo mariadb-secure-installation
```
        
![](https://i.imgur.com/mL3MAii.png)
        
![](https://i.imgur.com/6oQbaZl.png)

```console
mysql -u root -p
```
Utilisation d'un générateur de mot de passe sécurisé pour configurer l'accès au service administrateur de la base de donnée de nextcloud.  
        
![](https://i.imgur.com/5rHznoX.png)
    
![](https://i.imgur.com/cHiZxMD.png)
    
![](https://i.imgur.com/MGCoN9p.png)
        
Il faut changer l'adresse IP de bind de MariaDB :
```console
sudo nano /etc/mysql/mariadb.conf.d/*-server.cnf
```
![](https://i.imgur.com/dBIo5Jg.png)     

Et il faut changer l'adresse IP par l'adresse locale de la machine 10.0.4.5 à la page 4 du fichier .cnf.

![](https://i.imgur.com/7YQbmbj.png)

![](https://i.imgur.com/jMld2uG.png)
 
    
    
[&#x23F6;](#top)
    
    
<div id=NouvDisque>    

### Configuration du nouveau disque

Modification du chemin d'accès à la base de données après le montage du nouveau disque sur **/database**
    
![](https://i.imgur.com/4GmzKZe.png)
    
![](https://i.imgur.com/3f11mFg.png)
 
    
[&#x23F6;](#top)
    
    
    
<div id=VMAppli>

## **Installation et configuration de la VM Application**

Connexion en SSH à la VM Applicative via la VM administration.

![](https://i.imgur.com/yvvl2uH.png)

<div id="NomDomaine">
   
### Affectation d'un Nom de Domaine sur la plateforme Azure    
Choix du nom de domaine pour l'IP publique de la VM applicative pour installer Nextcloud et le serveur Apache. Ceci afin que les utilisateurs puissent accéder à Nextcloud par un autre biais que l'adresse IP publique plus simple à utiliser.

![](https://i.imgur.com/B9hpy17.png)
     
![](https://i.imgur.com/7I8YkIz.png)
     
![](https://i.imgur.com/vvQlPGs.png)
 
    
    
[&#x23F6;](#top)
    
    

<div id="ApacheServer">
     
### Installation Apache Server

```console
sudo apt-get update
```

```console
sudo apt-get install -y apache2
```
    
![](https://i.imgur.com/rj4CR3h.png)

```console
systemctl enable apache2
Synchronizing state of apache2.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable apache2
```

```console
apache2ctl -v
Server version: Apache/2.4.51 (Debian)
Server built: 2021-10-07T17:49:44
```

```console
a2enmod rewrite
```

```console
a2enmod deflate
a2enmod headers
a2enmod ssl
```

```console
sudo systemctl reload apache2
```
    
[&#x23F6;](#top)
    


<div id=PortTcp>     
    
### Choix du numéro du port TCP
     
<div id=ApachePort>
    
#### **Sur Apache**

On ouvre le port d'écoute qui nous intéresse **8080** dans le fichier de configuration au niveau du **Virtual host**.
    
```console
nano /etc/apache2/sites-enabled/000-default.conf
```
![](https://i.imgur.com/8z3jOzn.png)
```console
sudo systemctl reload apache2
```
    
Pour ouvrir le port d'écoute :

```console
sudo nano /etc/apache2/ports.conf
```
![](https://i.imgur.com/A4FIG8g.png)

```console
sudo systemctl reload apache2
```
    
On vérifie les ports ouverts sur le serveur via la commande :
    
```console
ss -plnt
```

<div id=AzurePort>

#### **Sur Azure**

Dans l'onglet *Mise en réseau* de la VM application, dans les *Règles des ports d'entrée*, on autorise le port choisi sur le réseau Azure.    

![](https://i.imgur.com/zd6ay3F.png)
![](https://i.imgur.com/37r9qaf.png)

On vérifie que le serveur Apache est joignable via le navigateur avec l'adresse IP et le Nom de Domaine : 

![](https://i.imgur.com/lC57diq.png)
![](https://i.imgur.com/VmzGwGJ.png)
  
Ça fonctionne !
    
    
[&#x23F6;](#top)
    

    
<div id=NextcloudApp>

### Installation de PHP et NextCloud
        
```console
sudo apt install wget unzip
```
    
```console
sudo apt install libapache2-mod-php
```
    
Installation de PHP et de ses dépendances
    
```console
sudo apt install -y php php-gd php-curl php-zip php-dom php-xml php-simplexml php-mbstring php-mysql 
```

On se connecte à la VM BDD pour créer la base de donnée avec MariaDB qui est nécessaire à l'installation et la configuration du service Nextcloud.
    
```console
CREATE DATABASE nextclouddb;
GRANT ALL ON nextclouddb.* TO 'utilisateur'@'adresse IP locale' IDENTIFIED BY 'mot de passe';
FLUSH PRIVILEGES;
EXIT;
```

On se reconnecte sur la VM Applicative pour terminer l'installation de Nextcloud.
 
```console
cd /tmp
```

```console
wget https://download.nextcloud.com/server/releases/nextcloud-24.0.2.zip
```
        
```console
unzip nextcloud-24.0.2.zip
```
        
```console
sudo mv nextcloud /var/www/html
```
    
```console
sudo chown -R www-data:www-data /var/www/html/nextcloud
```
    
    
[&#x23F6;](#top)

 
<div id=ConfigChemin>  

### Configuration du chemin entre Mariadb et Nextcloud

Le fichier de configuration **config.php** va automatiquement se créer dans le dossier *var/www/html/nextcloud*. Un fichier exemple **config.sample.php** est disponible à titre d'exemple.
Une fois le fichier créé, on y ajoute les domaines autorisés :

```console
sudo nano config.php
```

```console
<?php
$CONFIG = array (
  'instanceid' => 'ocmn1inscl1l',
  'passwordsalt' => 'password',
  'secret' => 'secret',
  'trusted_domains' =>
  array (
    0 => '20.118.128.190:8080',
    1 => 'nextg4.westus3.cloudapp.azure.com:8080',
 ),
  'datadirectory' => '/nextcloud_data',
  'dbtype' => 'mysql',
  'version' => '24.0.2.1',
  'overwrite.cli.url' => 'http://20.118.128.190:8080/nextcloud',
  'dbname' => 'nextcloud',
  'dbhost' => '10.0.4.5:3306',
  'dbport' => '',
  'dbtableprefix' => 'oc_',
  'mysql.utf8mb4' => true,
  'dbuser' => 'groupe4',
  'dbpassword' => 'Password',
  'installed' => true,
); 
```
 
On va configurer Apache :

```console
/etc/apache2/sites-available
```

On ajoute le nom du serveur au fichier de configuration de **nextcloud.conf**

```console
touch nextcloud.conf
```
![](https://i.imgur.com/HhrTEM1.png)  


On active l'hôte virtuel avec cette ligne de commande
```console
a2ensite nextcloud.conf
```

On recharge Apache2 pour valider les modifications
```console
systemctl reload apache2
```


[&#x23F6;](#top)


<div id=nextcloud> 

## Configuration de Nextcloud

Connexion à Nextcloud pour la configuration via le navigateur web par l'adresse : http://nextg4.westus3.cloudapp.azure.com:8080/nextcloud 
  
<div id=connexionNC> 

### Création du compte administrateur de Nextcloud
![](https://i.imgur.com/tGq0Mks.png)

![](https://i.imgur.com/FF3Uk9R.png)
    
![](https://i.imgur.com/ZaYEVki.png)
    
![](https://i.imgur.com/fpD3ivr.png)

Tableau de bord administrateur :

![](https://i.imgur.com/Kjqu98K.png)

On peut créer des utilisateurs et des groupes depuis un compte administrateur.

![](https://i.imgur.com/Y93Wsei.png)

</br>
Création d'un utilisateur :

![](https://i.imgur.com/jRwZzBt.png)

Il est possible de créer un utilisateur directement avec les lignes de commandes suivante sur la VM Applicative :

![](https://i.imgur.com/5rs5rJY.png)


[&#x23F6;](#top)



<div id=nextcloud_user> 
 
### Interface utilisateur

Page d'accueil utilisateur **Dashboard**

![](https://i.imgur.com/pRhgwVB.png)

</br> 

Barre de navigation Nextcloud

![](https://i.imgur.com/ntKDgMW.png)

</br> 

Onglet *Files* qui rassemblent tous les fichiers disponibles pour l'utilisateur

![](https://i.imgur.com/T7benKS.png)

</br> 

Onglet *Photos* qui rassemble les médias disponibles pour l'utilisateur
![](https://i.imgur.com/eaX9UHD.png)
</br> 

Onglet *Activity* qui trie chronologiquement les actions réalisés sur Nextcloud
![](https://i.imgur.com/EzCn3sd.png)
</br> 
Onglet qui permet la recherche de n'importe quel élément
![](https://i.imgur.com/Gp5MuE9.png)

</br>

Onglet *Notifications* qui recense les événements

![](https://i.imgur.com/FtNR0UW.png)

</br>

Onglet *Contacts* qui recense tous les contacts lié à l'utilisateur et permet une recherche de contacts

![](https://i.imgur.com/0f7VcSj.png)

</br>

Menu de navigation du profil utilisateur

![](https://i.imgur.com/lBj937O.png)

</br>

Onglet *Settings* qui recense tous les paramètres modifiables par l'utilisateur

![](https://i.imgur.com/doVDBGV.png)

</br>

Onglet *Help* qui contient de la documentation à destination de l'utilisateur
![](https://i.imgur.com/6JE69Ug.png)



[&#x23F6;](#top)



</br>



<div id=AjoutFichier> 

### Ajout de fichiers

</br>
On peut ajouter un fichier depuis l'onglet *Files*

![](https://i.imgur.com/j7sUvPQ.png)

</br>


![](https://i.imgur.com/wDRNqju.png)
</br>

Le fichier est ainsi ajouté et disponible pour l'utilisateur

![](https://i.imgur.com/3yLFldB.png)

</br>

On peut manipuler le fichier et réaliser plusieurs actions (commenter, partager,...)

![](https://i.imgur.com/fA0pgzH.png)


[&#x23F6;](#top)


<div id=bonus>


## Bonus

<div id =bonusproxy>  

### Proxyjump pour l'accès des administrateurs aux VM:

Créer un fichier **config** dans le dossier **.ssh/** de notre pc portable.

![](https://i.imgur.com/JMJ8cHy.png)

Sous Windows, la ligne correspondant à *IdentityFile* pour **Host bastion** (VM Administration) n'est pas nécessaire et peut conduire à une ligne d'erreur  (permissions système).

![](https://i.imgur.com/BPYxGP7.png)

Ici pour se connectecter à la VM Administration, il suffit de taper **ssh bastion**.

![](https://i.imgur.com/kxS8rpL.png)

On peut également se connecter au serveur App avec **ssh app** et au serveur de base de donnée via **ssh bdd**.

<div id=certbot>

### Certificat TLS avec Certbot pour une connexion en HTTPS

<div id=cerbot_install>

#### **Installation et lancement de Certbot**

Installation de snapd pour gérer les paquets de certbot
```console
sudo snap install core; sudo snap refresh core
``` 
On installe certbot
```console
sudo snap install --classic certbot

```
On utilise cette ligne de commande pour s'assurer que certbot peut s'éxécuter
```console
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

On lance certbot grâce à la ligne de commande suivante qui va lancer la création du certificat SSL et de sa clé
```console
sudo certbot --apache
```


Ajout des chemins du certificat ssl généré sur le serveur Apache

</br>

![](https://i.imgur.com/YYeiMfd.png)

Ajout des paramètres suivants pour activer le SSLEngine dans le fichier de configuration d'Apache **000-default.conf** situé dans **/etc/apache2/sites-enabled**

```console
SSLEngine on
SSLCertificateFile /etc/letsencrypt/live/nextg4.westus3.cloudapp.azure.com/fullchain.pem
SSLCertificateKeyFile /etc/letsencrypt/live/nextg4.westus3.cloudapp.azure.com/privkey.pem

``` 

</br>

On peut vérifier en se connectant au site que la connexion est bien sécurisée en https

![](https://i.imgur.com/uAJV78P.png)

Le certificat est visible et accessible
![](https://i.imgur.com/ipepMR7.png)

</br> 

![](https://i.imgur.com/lRm8CRX.png)

</br>

![](https://i.imgur.com/KKxMitJ.png)

![](https://i.imgur.com/Bda1ENC.png)

[&#x23F6;](#top)
