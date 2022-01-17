# LDAP

```shell
sudo apt update
sudo apt install -y slapd ldap-utils certbot

sudo dpkg-reconfigure slapd # ldap.felicitass-sdi1.alphahorizon.io, felicitass-sdi1

curl ldaps://ldap.felicitass-sdi1.alphahorizon.io:443 # Test the connection

socat tcp-listen:8389,fork openssl:ldap.felicitass-sdi1.alphahorizon.io:443 # Run this on the local system to connect with Apache Directory Studio as the latter does not send a SNI header/is not TLS compliant
curl ldap://localhost:8389 # Test the proxy's connection

# Connect in Apache Directory Studio with the following info:
# Hostname: localhost
# Port: 8389
# Bind DN or user: cn=admin,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
# Bind password: The password from `sudo dpkg-reconfigure slapd`

# Connect with ldapwhoami like so:
ldapwhoami -H 'ldaps://ldap.felicitass-sdi1.alphahorizon.io:443' -x # Anonymous
ldapwhoami -H 'ldaps://ldap.felicitass-sdi1.alphahorizon.io:443' -W -D cn=admin,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io # As admin

# Now add the objects (you can create a password hash using `slappasswd | base64`):
ldapadd -H 'ldaps://ldap.felicitass-sdi1.alphahorizon.io:443' -W -D cn=admin,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io <<'EOT'
version: 1

dn: dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: dcObject
objectClass: organization
objectClass: top
dc: ldap
o: felicitass-sdi1

# We already set this up using `dpkg-reconfigure`
# dn: cn=admin,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
# objectClass: organizationalRole
# objectClass: simpleSecurityObject
# cn: admin
# userPassword:: e1NTSEF9cEhFK0VQT0cyZ3lSeU9nanZGcXNXT2I1ekdzR2w5Q0Q=
# description: LDAP administrator

dn: ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: departments

dn: ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: software

dn: ou=financial,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: financial

dn: ou=devel,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: devel

dn: ou=testing,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: testing

dn: ou=ops,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: ops

dn: uid=bean,ou=devel,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Audrey Bean
sn: Bean
givenName: Audrey
mail: bean@ldap.felicitass-sdi1.alphahorizon.io
uid: bean
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8=

dn: uid=smith,ou=devel,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Jane Smith
sn: Smith
givenName: Jane
mail: smith@ldap.felicitass-sdi1.alphahorizon.io
uid: smith
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8=

dn: uid=waibel,ou=financial,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: posixAccount
objectClass: top
cn: Jakob Waibel
gidNumber: 100
homeDirectory: /usr/jakob
sn: Waibel
uid: waibel
uidNumber: 1337
givenName: Jakob
mail: waibel@ldap.felicitass-sdi1.alphahorizon.io
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8=

dn: uid=simpson,ou=financial,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Homer Simpson
sn: Simpson
givenName: Homer
mail: simpson@ldap.felicitass-sdi1.alphahorizon.io
uid: simpson
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8=

dn: uid=pojtinger,ou=testing,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Felicitas Pojtinger
sn: Pojtinger
givenName: Felicitas
mail: pojtinger@ldap.felicitass-sdi1.alphahorizon.io
uid: pojtinger
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8=

dn: uid=simpson,ou=testing,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Maggie Simpson
sn: Simpson
givenName: Maggie
mail: simpson@ldap.felicitass-sdi1.alphahorizon.io
uid: simpson
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8=

dn: uid=aleimut,ou=devel,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Adelheit Aleimut
sn: Aleimut
givenName: Adelheit
mail: aleimut@ldap.felicitass-sdi1.alphahorizon.io
uid: aleimut
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8=

dn: uid=tibbie,ou=testing,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: posixAccount
objectClass: top
cn: Oswald Tibbie
gidNumber: 100
homeDirectory: /usr/oswald
sn: Tibbie
uid: tibbie
uidNumber: 1234
givenName: Oswald
mail: tibbie@ldap.felicitass-sdi1.alphahorizon.io
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8=

dn: uid=operator,ou=ops,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: posixAccount
objectClass: top
cn: Operator Operatis
gidNumber: 100
homeDirectory: /usr/operator
sn: Operator
uid: operatis
uidNumber: 1235
givenName: Operator
mail: operator@ldap.felicitass-sdi1.alphahorizon.io
userPassword:: e1NTSEF9Y1dtYUZ5Zi9HSTNTcFYyaktmYlpieUhEdFh5ek5wVEkK
EOT

# And test if we can access using a user
ldapwhoami -H 'ldaps://ldap.felicitass-sdi1.alphahorizon.io:443' -W -D uid=bean,ou=devel,ou=software,ou=departments,dc=ldap,dc=felicitass-sdi1,dc=alphahorizon,dc=io # As bean using password "password"
```
