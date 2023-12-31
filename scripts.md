VM Administration

```code
ssh-keygen -b 4096 -t rsa
nano .ssh/authorized_keys
sudo nano /etc/ssh/sshd_config
sudo systemctl restart sshd
ss -plnt
```

VM Base De Données (BDD)
```code
sudo apt -y update
sudo apt -y install curl software-properties-common gnupg2
sudo apt -y upgrade
sudo reboot
curl -LsS -O https://downloads.mariadb.com/MariaDB/mariadb_repo_setup
sudo bash mariadb_repo_setup --mariadb-server-version=10.8.3
sudo apt install mariadb-server mariadb-client
sudo mariadb-secure-installation
mysql -u root -p
sudo nano /etc/mysql/mariadb.conf.d/*-server.cnf
```

VM Application (Nextcloud)
```code
sudo apt-get update
sudo apt-get install -y apache2
systemctl enable apache2
apache2ctl -v
a2enmod rewrite
a2enmod deflate
a2enmod headers
a2enmod ssl
sudo systemctl reload apache2
nano /etc/apache2/sites-enabled/000-default.conf
sudo systemctl reload apache2
sudo nano /etc/apache2/ports.conf
sudo systemctl reload apache2
sudo apt install wget unzip
sudo apt install libapache2-mod-php
# Installation des extensions PHP nécessaires
cd /tmp
wget https://download.nextcloud.com/server/releases/nextcloud-24.0.2.zip
unzip nextcloud-24.0.2.zip
sudo mv nextcloud /var/www/html
sudo chown -R www-data:www-data /var/www/html/nextcloud
sudo nano /var/www/html/nextcloud/config/config.php
sudo snap install core; sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --apache
# Ajouter les configurations SSL dans le fichier de configuration Apache
