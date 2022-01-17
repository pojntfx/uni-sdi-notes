# MariaDB

```shell
sudo apt update
sudo apt install -y mariadb-server
sudo mysql_secure_installation # Empty string, y, y, yourpassword, yourpassword, y, y, y, y

sudo mysql -u root -e 'GRANT ALL PRIVILEGES ON *.* TO 'phpmyadmin'@'localhost' WITH GRANT OPTION;'

sudo apt install -y phpmyadmin libapache2-mod-php # apache2, y, yourpassword, yourpassword
sudo phpenmod mbstring

sudo a2disconf phpmyadmin

sudo tee /etc/apache2/sites-available/phpmyadmin.felicitass-sdi1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName felicitass-sdi1.alphahorizon.io
        ServerAlias phpmyadmin.felicitass-sdi1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /usr/share/phpmyadmin

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/usr/share/phpmyadmin">
            Options SymLinksIfOwnerMatch
            DirectoryIndex index.php

            # limit libapache2-mod-php to files and directories necessary by pma
            <IfModule mod_php7.c>
                php_admin_value upload_tmp_dir /var/lib/phpmyadmin/tmp
                php_admin_value open_basedir /usr/share/phpmyadmin/:/usr/share/doc/phpmyadmin/:/etc/phpmyadmin/:/var/lib/phpmyadmin/:/usr/share/php/:/usr/share/javascript/
            </IfModule>
        </Directory>

        # Disallow web access to directories that don't need it
        <Directory "/usr/share/phpmyadmin/templates">
            Require all denied
        </Directory>
        <Directory "/usr/share/phpmyadmin/libraries">
            Require all denied
        </Directory>
</VirtualHost>
EOT
sudo a2ensite phpmyadmin.felicitass-sdi1.alphahorizon.io
sudo systemctl reload apache2

# Now visit https://phpmyadmin.felicitass-sdi1.alphahorizon.io/ and login as root using yourpassword
```
