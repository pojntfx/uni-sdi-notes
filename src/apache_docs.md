# Apache Docs

```shell
ssh jean@sdi-1.alphahorizon.io

sudo apt update
sudo apt install -y apache2 tree

sudo tee /etc/apache2/ports.conf <<'EOT'
Listen 8080
EOT

sudo systemctl enable --now apache2
sudo systemctl restart apache2

curl https://apache.sdi-1.alphahorizon.io/ # Test Apache's default page

sudo tree -T "Example Index" -H '.' -o /var/www/html/index.html /var/www/html # Replace the default file with a file listing
curl https://apache.sdi-1.alphahorizon.io/ # Test the generated index

sudo mkdir -p /var/www/sdidoc
sudo tree -T "Example Index For sdidoc" -H '.' -o /var/www/sdidoc/index.html /var/www/html # Create a secondary listing for the alias
sudo tee /etc/apache2/sites-available/apache.sdi-1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName sdi-1.alphahorizon.io
        ServerAlias apache.sdi-1.alphahorizon.io

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
sudo a2ensite apache.sdi-1.alphahorizon.io
sudo systemctl reload apache2
curl https://apache.sdi-1.alphahorizon.io/sdidoc/ # Test the generated index in `/sdidoc`

sudo apt install -y apache2-doc
curl https://apache.sdi-1.alphahorizon.io/manual/en/index.html # Access the installed docs; this applies to all virtual hosts

sudo mkdir -p /var/www/marx.apache.sdi-1.alphahorizon.io
echo '<h1>Marx</h1>' | sudo tee /var/www/marx.apache.sdi-1.alphahorizon.io/index.html
sudo tee /etc/apache2/sites-available/marx.apache.sdi-1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName sdi-1.alphahorizon.io
        ServerAlias marx.apache.sdi-1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /var/www/marx.apache.sdi-1.alphahorizon.io

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/marx.apache.sdi-1.alphahorizon.io">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>
</VirtualHost>
EOT
sudo a2ensite marx.apache.sdi-1.alphahorizon.io
sudo systemctl reload apache2
curl https://marx.apache.sdi-1.alphahorizon.io/ # Test the Marx site

sudo mkdir -p /var/www/kropotkin.apache.sdi-1.alphahorizon.io
echo '<h1>Kropotkin</h1>' | sudo tee /var/www/kropotkin.apache.sdi-1.alphahorizon.io/index.html
sudo tee /etc/apache2/sites-available/kropotkin.apache.sdi-1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName sdi-1.alphahorizon.io
        ServerAlias kropotkin.apache.sdi-1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /var/www/kropotkin.apache.sdi-1.alphahorizon.io

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/kropotkin.apache.sdi-1.alphahorizon.io">
                Options Indexes FollowSymLinks
                AllowOverride None
                Require all granted
        </Directory>
</VirtualHost>
EOT
sudo a2ensite kropotkin.apache.sdi-1.alphahorizon.io
sudo systemctl reload apache2
curl https://kropotkin.apache.sdi-1.alphahorizon.io/ # Test the Kropotkin site

sudo mkdir -p /var/www/secure.apache.sdi-1.alphahorizon.io
echo '<h1>Super secure content!</h1>' | sudo tee /var/www/secure.apache.sdi-1.alphahorizon.io/index.html
sudo tee /etc/apache2/sites-available/secure.apache.sdi-1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName sdi-1.alphahorizon.io
        ServerAlias secure.apache.sdi-1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /var/www/secure.apache.sdi-1.alphahorizon.io

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/var/www/secure.apache.sdi-1.alphahorizon.io">
                Options Indexes FollowSymLinks
                AllowOverride None

                AuthType Basic
                AuthBasicProvider ldap
                AuthName "Please enter your LDAP username and password"
                AuthLDAPURL "ldap://localhost:389/ou=devel,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io"
                Require valid-user
        </Directory>
</VirtualHost>
EOT
sudo a2enmod authnz_ldap
sudo a2ensite secure.apache.sdi-1.alphahorizon.io
sudo systemctl reload apache2

ldapwhoami -H 'ldaps://ldap.sdi-1.alphahorizon.io:443' -W -D uid=bean,ou=devel,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io # Connect as bean using ldapwhoami (using password "password")

curl https://secure.apache.sdi-1.alphahorizon.io/ # Try to access the secure site anonymously (401 Unauthorized)

curl -u bean:password https://secure.apache.sdi-1.alphahorizon.io/ # Access the secure site as bean (or anyone else in ou=devel,ou=software) with password "password" (you can also open it using a browser)
```
