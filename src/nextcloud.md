# Nextcloud

```shell
sudo apt update
sudo apt install -y libapache2-mod-php php-ctype php-curl php-dom php-gd php-iconv php-json php-xml php-mbstring php-posix php-simplexml php-xmlreader php-xmlwriter php-zip php-mysql php-fileinfo php-bz2 php-intl php-ldap php-ftp php-imap php-bcmath php-gmp php-exif php-apcu php-imagick php-phar ffmpeg libreoffice curl unzip

sudo mysql -u root -e "CREATE USER 'nextcloud'@'localhost' IDENTIFIED BY 'mypasswd';"
sudo mysql -u root -e "CREATE DATABASE nextcloud;"
sudo mysql -u root -e "GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud'@'localhost';"
sudo mysql -u root -e "FLUSH PRIVILEGES;"

sudo tee /etc/php/*/apache2/php.ini <<'EOT'
date.timezone = Europe/Berlin
memory_limit = 1024M
upload_max_filesize = 1024M
post_max_size = 1024M
max_execution_time = 300
EOT

curl -Lo /tmp/nextcloud.zip https://download.nextcloud.com/server/releases/nextcloud-23.0.0.zip
unzip /tmp/nextcloud.zip 'nextcloud/*' -d /tmp/nextcloud
sudo mv /tmp/nextcloud/nextcloud/ /var/www/nextcloud.felixs-sdi1.alphahorizon.io/
sudo chown -R www-data:www-data /var/www/nextcloud.felixs-sdi1.alphahorizon.io/
sudo chmod -R 755 /var/www/nextcloud.felixs-sdi1.alphahorizon.io/

sudo tee /etc/apache2/sites-available/nextcloud.felixs-sdi1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName felixs-sdi1.alphahorizon.io
        ServerAlias nextcloud.felixs-sdi1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /var/www/nextcloud.felixs-sdi1.alphahorizon.io

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/nextcloud.felixs-sdi1.alphahorizon.io">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>
</VirtualHost>
EOT
sudo a2ensite nextcloud.felixs-sdi1.alphahorizon.io
sudo systemctl reload apache2

curl https://nextcloud.felixs-sdi1.alphahorizon.io/ # Access the Nextcloud installer

# Now visit https://nextcloud.felixs-sdi1.alphahorizon.io/index.php and create an admin account
# - Set `nextcloud` as the database user
# - Set `mypasswd` as the database password
# - Set `nextcloud` as the database name
# - Set `localhost:3306` as the database host
# - Click on "finish setup"
# - Visit https://nextcloud.felixs-sdi1.alphahorizon.io/index.php/settings/admin/richdocuments and select `Use the built-in CODE - Collabora Online Development Edition`
# - Visit https://nextcloud.felixs-sdi1.alphahorizon.io/index.php/settings/apps/installed/user_ldap and click `Enable`
# - Visit https://nextcloud.felixs-sdi1.alphahorizon.io/index.php/settings/admin/ldap and enter the following:
# - Set `localhost` as the host
# - Set `389` as the port
# - Set `cn=admin,dc=ldap,dc=felixs-sdi1,dc=alphahorizon,dc=io` as the user DN
# - Set the password from `sudo dpkg-reconfigure slapd` as the password
# - Set `dc=ldap,dc=felixs-sdi1,dc=alphahorizon,dc=io` as the base DN
# Click verify, "continue", verify and "continue"
# - Set `organizationUnit` under `Only these object classes:` in the groups settings
# - Click `Verify settings and count the groups`
# - Visit https://nextcloud.felixs-sdi1.alphahorizon.io/index.php/login and login as bean using password "password" (if you can't login, go back to https://nextcloud.felixs-sdi1.alphahorizon.io/index.php/settings/admin/ldap and verify everything again)
```
