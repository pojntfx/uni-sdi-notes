# LDAP

```shell
ssh jean@sdi-1.alphahorizon.io

sudo apt update
sudo apt install -y slapd ldap-utils certbot

sudo dpkg-reconfigure slapd
# Omit server configuration: No
# DNS domain name: ldap.sdi-1.alphahorizon.io
# Organization name: sdi-1
# Do you want the DB to be purged: No
# Move old database: Yes

curl ldaps://ldap.sdi-1.alphahorizon.io:443 # Connect anonymously using cURL
ldapwhoami -H 'ldaps://ldap.sdi-1.alphahorizon.io:443' -x # Connect anonymous using ldapwhoami
ldapwhoami -H 'ldaps://ldap.sdi-1.alphahorizon.io:443' -W -D cn=admin,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io # Connect as admin using ldapwhoami

socat tcp-listen:8389,fork openssl:ldap.sdi-1.alphahorizon.io:443 # Create local TLS termination proxy to work around Apache Directory Studio's broken SNI implementation
curl ldap://localhost:8389 # Test the local proxy using ldapwhoami
# Test the local proxy using Apache Directory Studio:
# Hostname: localhost
# Port: 8389
# Bind DN or user: cn=admin,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
# Bind password: The password from `sudo dpkg-reconfigure slapd`

ldapadd -H 'ldaps://ldap.sdi-1.alphahorizon.io:443' -W -D cn=admin,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io <<'EOT'
version: 1

# We already set these up using `dpkg-reconfigure`

# dn: dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
# objectClass: dcObject
# objectClass: organization
# objectClass: top
# dc: ldap
# o: sdi-1

# dn: cn=admin,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
# objectClass: organizationalRole
# objectClass: simpleSecurityObject
# cn: admin
# userPassword:: e1NTSEF9cEhFK0VQT0cyZ3lSeU9nanZGcXNXT2I1ekdzR2w5Q0Q= # sudo slappasswd | base64
# description: LDAP administrator

dn: ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: departments

dn: ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: software

dn: ou=financial,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: financial

dn: ou=devel,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: devel

dn: ou=testing,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: testing

dn: ou=ops,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: organizationalUnit
objectClass: top
ou: ops

dn: uid=bean,ou=devel,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Audrey Bean
sn: Bean
givenName: Audrey
mail: bean@ldap.sdi-1.alphahorizon.io
uid: bean
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64

dn: uid=smith,ou=devel,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Jane Smith
sn: Smith
givenName: Jane
mail: smith@ldap.sdi-1.alphahorizon.io
uid: smith
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64

dn: uid=waibel,ou=financial,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
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
mail: waibel@ldap.sdi-1.alphahorizon.io
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64

dn: uid=simpson,ou=financial,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Homer Simpson
sn: Simpson
givenName: Homer
mail: simpson@ldap.sdi-1.alphahorizon.io
uid: simpson
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64

dn: uid=doe,ou=testing,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Jean Doe
sn: Doe
givenName: Jean
mail: doe@ldap.sdi-1.alphahorizon.io
uid: doe
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64

dn: uid=simpson,ou=testing,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Maggie Simpson
sn: Simpson
givenName: Maggie
mail: simpson@ldap.sdi-1.alphahorizon.io
uid: simpson
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64

dn: uid=aleimut,ou=devel,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
cn: Adelheit Aleimut
sn: Aleimut
givenName: Adelheit
mail: aleimut@ldap.sdi-1.alphahorizon.io
uid: aleimut
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64

dn: uid=tibbie,ou=testing,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
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
mail: tibbie@ldap.sdi-1.alphahorizon.io
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64

dn: uid=operator,ou=ops,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io
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
mail: operator@ldap.sdi-1.alphahorizon.io
userPassword:: e1NTSEF9NGxCMnc4dThQRXI5Rjd3VGZjN3ltNWkwUDk5N3dOeS8= # sudo slappasswd | base64
EOT

ldapwhoami -H 'ldaps://ldap.sdi-1.alphahorizon.io:443' -W -D uid=bean,ou=devel,ou=software,ou=departments,dc=ldap,dc=sdi-1,dc=alphahorizon,dc=io # Connect as bean using ldapwhoami (using password "password")
```
