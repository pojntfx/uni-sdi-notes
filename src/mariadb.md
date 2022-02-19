# MariaDB

MariaDB is a community-developed, commercially supported fork of the MySQL relational database management system (RDBMS), intended to remain free and open-source software under the GNU General Public License[^note].

```shell
ssh jean@sdi-1.alphahorizon.io

sudo apt update
sudo apt install -y mariadb-server

sudo mysql_secure_installation
# Current password for root: Press enter
# Switch to unix_socket authentication: y
# Change the root password: y
# New password: yourpassword
# Re-enter new password: yourpassword
# Remove anonymous users: y
# Disallow root login remotely: y
# Remove test database and access to it: y
# Reload privilege tables now: y

sudo apt install -y phpmyadmin libapache2-mod-php
# Web server: apache2
# Configure database: Yes
# MySQL application password for phpmyadmin: yourpassword
# Password confirmation: yourpassword

sudo a2disconf phpmyadmin
sudo mysql -u root -e "GRANT ALL PRIVILEGES ON *.* TO 'phpmyadmin'@'localhost' WITH GRANT OPTION;"
sudo phpenmod mbstring

sudo tee /etc/apache2/sites-available/phpmyadmin.sdi-1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName sdi-1.alphahorizon.io
        ServerAlias phpmyadmin.sdi-1.alphahorizon.io

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
sudo a2ensite phpmyadmin.sdi-1.alphahorizon.io
sudo systemctl reload apache2
curl https://phpmyadmin.sdi-1.alphahorizon.io/ # Test the phpMyAdmin site

# Visit https://phpmyadmin.sdi-1.alphahorizon.io/ and login as root using yourpassword
```

[^note]: From Wikipedia, last checked 2022-02-19 ([https://en.wikipedia.org/wiki/MariaDB](https://en.wikipedia.org/wiki/MariaDB))
