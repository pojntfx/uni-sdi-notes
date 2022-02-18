# LDAP Account Manager

```shell
ssh jean@sdi-1.alphahorizon.io

sudo apt update
sudo apt install -y ldap-account-manager

sudo a2disconf ldap-account-manager

sudo tee /etc/apache2/sites-available/ldap-account-manager.sdi-1.alphahorizon.io.conf <<'EOT'
<VirtualHost *:8080>
        ServerName sdi-1.alphahorizon.io
        ServerAlias ldap-account-manager.sdi-1.alphahorizon.io

        ServerAdmin webmaster@alphahorizon.io
        DocumentRoot /usr/share/ldap-account-manager

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory "/usr/share/ldap-account-manager">
          Options +FollowSymLinks
          AllowOverride All
          Require all granted
          DirectoryIndex index.html
        </Directory>

        <Directory "/var/lib/ldap-account-manager/tmp">
          Options -Indexes
        </Directory>

        <Directory "/var/lib/ldap-account-manager/tmp/internal">
          Options -Indexes
          Require all denied
        </Directory>

        <Directory "/var/lib/ldap-account-manager/sess">
          Options -Indexes
          Require all denied
        </Directory>

        <Directory "/var/lib/ldap-account-manager/config">
          Options -Indexes
          Require all denied
        </Directory>

        <Directory "/usr/share/ldap-account-manager/lib">
          Options -Indexes
          Require all denied
        </Directory>

        <Directory "/usr/share/ldap-account-manager/help">
          Options -Indexes
          Require all denied
        </Directory>

        <Directory "/usr/share/ldap-account-manager/locale">
          Options -Indexes
          Require all denied
        </Directory>
</VirtualHost>
EOT
sudo a2ensite ldap-account-manager.sdi-1.alphahorizon.io
sudo systemctl reload apache2

# Visit https://ldap-account-manager.sdi-1.alphahorizon.io/templates/config/mainlogin.php, login with `lam` as the master password
# - Don't encrypt session
# - Use `ldap://localhost:389/` as the server (where `ldaps://` is the default)
# - Set the new master password

# Visit https://ldap-account-manager.sdi-1.alphahorizon.io/templates/config/confmain.php, and login with `lam` as the profile password
# - Set `dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io` as the tree suffix
# - Set `cn=admin,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io` as the list of valid users
# - Set SSH key file to empty string
# - Set the profile password to yourpassword
# - Set Users LDAP suffix under "Account types" to `ou=devel,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io`
# - Delete "groups" under "Account types"

# Visit https://ldap-account-manager.sdi-1.alphahorizon.io/templates/login.php and login with your the LDAP admin account password
```
