# Apache

```shell
sudo apt update
sudo apt install -y apache2
sudo vi /etc/apache2/ports.conf # Now replace/add the following:
Listen 8080
sudo systemctl restart apache2
sudo systemctl enable --now apache2
sudo systemctl status apache2

sudo tree -T "Example Index" -H '.' -o /var/www/html/index.html /var/www/html # Replace the default file with a file listing
sudo mkdir -p /var/www/sdidoc
sudo tree -T "Example Index For sdidoc" -H '.' -o /var/www/sdidoc/index.html /var/www/html # Create a secondary listing for the alias
sudo tee /etc/apache2/sites-available/apache.felicitass-sdi1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName felicitass-sdi1.alphahorizon.io
        ServerAlias apache.felicitass-sdi1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/html">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>

        Alias /sdidoc /var/www/sdidoc

        <Directory "/var/www/sdidoc">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>
</VirtualHost>
EOT
sudo a2dissite 000-default.conf
sudo a2ensite apache.felicitass-sdi1.alphahorizon.io
sudo systemctl reload apache2

curl https://apache.felicitass-sdi1.alphahorizon.io/ # Access the index
curl https://apache.felicitass-sdi1.alphahorizon.io/sdidoc/ # Access the index in `/var/www/sdidoc`

sudo apt install -y apache2-doc # Install the docs package
curl https://apache.felicitass-sdi1.alphahorizon.io/manual/en/index.html # Access the installed docs; be careful, this applies to all virtual hosts

sudo mkdir -p /var/www/marx.apache.felicitass-sdi1.alphahorizon.io
echo '<h1>Marx</h1>' | sudo tee /var/www/marx.apache.felicitass-sdi1.alphahorizon.io/index.html
sudo tee /etc/apache2/sites-available/marx.apache.felicitass-sdi1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName felicitass-sdi1.alphahorizon.io
        ServerAlias marx.apache.felicitass-sdi1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /var/www/marx.apache.felicitass-sdi1.alphahorizon.io

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/marx.apache.felicitass-sdi1.alphahorizon.io">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>
</VirtualHost>
EOT
sudo a2ensite marx.apache.felicitass-sdi1.alphahorizon.io
sudo systemctl reload apache2

curl https://marx.apache.felicitass-sdi1.alphahorizon.io/ # Access the Marx site

sudo mkdir -p /var/www/kropotkin.apache.felicitass-sdi1.alphahorizon.io
echo '<h1>Kropotkin</h1>' | sudo tee /var/www/kropotkin.apache.felicitass-sdi1.alphahorizon.io/index.html
sudo tee /etc/apache2/sites-available/kropotkin.apache.felicitass-sdi1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName felicitass-sdi1.alphahorizon.io
        ServerAlias kropotkin.apache.felicitass-sdi1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /var/www/kropotkin.apache.felicitass-sdi1.alphahorizon.io

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/kropotkin.apache.felicitass-sdi1.alphahorizon.io">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>
</VirtualHost>
EOT
sudo a2ensite kropotkin.apache.felicitass-sdi1.alphahorizon.io
sudo systemctl reload apache2

curl https://kropotkin.apache.felicitass-sdi1.alphahorizon.io/ # Access the Kropotkin site

sudo mkdir -p /var/www/secure.apache.felicitass-sdi1.alphahorizon.io
echo '<h1>Super secure content!</h1>' | sudo tee /var/www/secure.apache.felicitass-sdi1.alphahorizon.io/index.html
sudo tee /etc/apache2/sites-available/secure.apache.felicitass-sdi1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName felicitass-sdi1.alphahorizon.io
        ServerAlias secure.apache.felicitass-sdi1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /var/www/secure.apache.felicitass-sdi1.alphahorizon.io

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/secure.apache.felicitass-sdi1.alphahorizon.io">
                Options Indexes FollowSymLinks
                AllowOverride None

                AuthType Basic
                AuthBasicProvider ldap
                AuthName "Please enter your LDAP username and password"
                AuthLDAPURL "ldap://localhost:389/ou=devel,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io"
                Require valid-user
        </Directory>
</VirtualHost>
EOT
sudo a2enmod authnz_ldap
sudo a2ensite secure.apache.felicitass-sdi1.alphahorizon.io
sudo systemctl reload apache2

ldapwhoami -H 'ldap://localhost:389' -W -D uid=bean,ou=devel,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io # Connect to local LDAP server as bean using password "password"

curl https://secure.apache.felicitass-sdi1.alphahorizon.io/ # Try to access the secure site anonymously (401 Unauthorized)
curl -u bean:password https://secure.apache.felicitass-sdi1.alphahorizon.io/ # Access the secure site as bean (or anyone else in ou=devel,ou=software) with password "password" (you can also open it using a browser)
```
