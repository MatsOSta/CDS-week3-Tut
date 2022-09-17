# CDS-week3-Tut

#Setting up your own CA
CA // CERTIFICATE AUTHORITY 
TLS certificates often SSL certificates by accident are avaliable for free or for a fee

sudo apt install easy-rsa
Using EASY rsa as a CA
https://easy-rsa.readthedocs.io/en/latest/#using-easy-rsa-as-a-ca
#Install and symlink
==
mkdir ~/easy-rsa
using ln to symlink updates
==
ln -s /usr/share/easy-rsa/* ~/easy-rsa/
changing permissions to allow only owner to access
==
chmod 700 ~/easy-rsa
install PKI inside easy-rsa dir
==
cd ~/easy-rsa
./easyrsa init-pki
//
init-pki complete; you may now create a CA or requests.
Your newly created PKI dir is: /home/ubadmin/easy-rsa/pki
//

#CA building
create and fill vars file
set_var EASYRSA_REQ_COUNTRY "NO"
set_var EASYRSA_REQ_PROVINCE "Agder"
set_var EASYRSA_REQ_CITY "Kristiansand"
set_var EASYRSA_REQ_ORG "UC3CND101"
set_var EASYRSA_REQ_EMAIL "barry.irwin@stud.noroff.no"
set_var EASYRSA_REQ_OU "Community"
set_var EASYRSA_ALGO "ec"
set_var EASYRSA_DIGEST "sha512"

~./easy-rsa/ ./easyrsa build-ca
## password : ubadmin
## name : ubserver
CA certificate file for publishing is at /home/ubadmin/easy-rsa/pki/ca.crt
ca.crt is the CA’s public certificate file. Users, servers, and clients will use this certificate to verify that they
are part of the same web of trust. Every user and server that uses your CA will need to have a copy of this
file. All parties will rely on the public certificate to ensure that someone is not impersonating a system and
performing a Man-in-the-middle attack.
ca.key is the private key that the CA uses to sign certificates for servers and clients. If an attacker gains
access to your CA and, in turn, your ca.key file, you will need to destroy your CA. This is why your ca.key
file should only be on your CA machine and that, ideally, your CA machine should be kept offline when not
signing certificate requests as an extra security measure

## Generating a CSR
CSR // Certificate Signing Request

mkdir to create directory and sperate paths
use openSSL CSR wizard to create cli syntax
https://www.digicert.com/easy-csr/openssl.htm

import name into CA system
~/easy-rsa$ ./easyrsa import-req ../webserver.csr webserver

CN // Common Name
SN // Short Name
~/easy-rsa/certs/apache_webserver // CN == blog.com SN == blog.com
~/easy-rsa/certs/MatSta66614@stud.noroff.no // CN == MatSta66614@stud.noroff.no SN == MatSta
~/easy-rsa/certs/mysqlDBserver // CN == ubserver SN == ubserver
~/easy-rsa/certs/recovery@ubserver // CN == recovery SN == recovery

CSR imported now sign
server // for servers
client // for clients

~/easy-rsa$ ./easyrsa sign-req server webserver

/home/ubadmin/easy-rsa/pki/issued/blog.com.crt
/home/ubadmin/easy-rsa/pki/issued/ubserver.crt
/home/ubadmin/easy-rsa/pki/issued/MatSta.crt
/home/ubadmin/easy-rsa/pki/issued/recovery.crt
# Might need to be copied back to where the key was generated and linked to server configuration??
# END 1

## Apache TLS
See tut

