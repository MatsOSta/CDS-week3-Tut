# CDS-week3-Tut

# Setting up your own CA
CA // CERTIFICATE AUTHORITY 
TLS certificates often SSL certificates by accident are avaliable for free or for a fee

sudo apt install easy-rsa
Using EASY rsa as a CA
https://easy-rsa.readthedocs.io/en/latest/#using-easy-rsa-as-a-ca
# Install and symlink
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

# CA building
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

## TLS tuning
# See differences between two ciphersuites
write sslscan output to file
sudo sslscan www.noroff.no:443 > www.n.txt
sudo sslscan noroff.no:443 > www.txt

grep or diff it.
grep -Fxnvf www.n.txt www.txt
diff www.n.txt www.txt
4c4
< Connected to 52.212.52.84
---
> Connected to 194.63.248.52
6c6
< Testing SSL server www.noroff.no on port 443 using SNI name www.noroff.no
---
> Testing SSL server noroff.no on port 443 using SNI name noroff.no
31,32c31
< Preferred TLSv1.3  128 bits  TLS_AES_128_GCM_SHA256        Curve 25519 DHE 253
< Accepted  TLSv1.3  256 bits  TLS_AES_256_GCM_SHA384        Curve 25519 DHE 253
---
> Preferred TLSv1.3  256 bits  TLS_AES_256_GCM_SHA384        Curve 25519 DHE 253
34,38c33,43
< Preferred TLSv1.2  128 bits  ECDHE-RSA-AES128-GCM-SHA256   Curve 25519 DHE 253
< Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-GCM-SHA384   Curve 25519 DHE 253
< Accepted  TLSv1.2  256 bits  ECDHE-RSA-CHACHA20-POLY1305   Curve 25519 DHE 253
< Accepted  TLSv1.2  128 bits  AES128-GCM-SHA256            
< Accepted  TLSv1.2  256 bits  AES256-GCM-SHA384            
---
> Accepted  TLSv1.3  128 bits  TLS_AES_128_GCM_SHA256        Curve 25519 DHE 253
> Preferred TLSv1.2  256 bits  ECDHE-RSA-AES256-GCM-SHA384   Curve 25519 DHE 253
> Accepted  TLSv1.2  128 bits  ECDHE-RSA-AES128-GCM-SHA256   Curve 25519 DHE 253
> Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-GCM-SHA384     DHE 2048 bits
> Accepted  TLSv1.2  128 bits  DHE-RSA-AES128-GCM-SHA256     DHE 2048 bits
> Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-SHA384       Curve 25519 DHE 253
> Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-SHA          Curve 25519 DHE 253
> Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-CCM8           DHE 2048 bits
> Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-CCM            DHE 2048 bits
> Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-SHA256         DHE 2048 bits
> Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-SHA            DHE 2048 bits
41a47,48
> TLSv1.3  192 bits  secp384r1 (NIST P-384)
> TLSv1.3  260 bits  secp521r1 (NIST P-521)
42a50
> TLSv1.3  224 bits  x448
43a52,53
> TLSv1.2  192 bits  secp384r1 (NIST P-384)
> TLSv1.2  260 bits  secp521r1 (NIST P-521)
44a55
> TLSv1.2  224 bits  x448
50,52c61,63
< Subject:  *.noroff.no
< Altnames: DNS:*.noroff.no, DNS:noroff.no
< Issuer:   DigiCert TLS RSA SHA256 2020 CA1
---
> Subject:  noroff.no
> Altnames: DNS:noroff.no
> Issuer:   R3
54,55c65,66
< Not valid before: Sep 27 00:00:00 2021 GMT
< Not valid after:  Oct 27 23:59:59 2022 GMT
---
> Not valid before: Aug 24 21:42:28 2022 GMT
> Not valid after:  Nov 22 21:42:27 2022 GMT




useful site for suites
https://ssl-config.mozilla.org/#server=apache&version=2.4.52&config=modern&openssl=3.0.2&guideline=5.6
sslscan 192.168.229.131:443
sudo nano /etc/apache2/mods-available/ssl.conf

Moodle
SSLv2     disabled
SSLv3     disabled
TLSv1.0   disabled
TLSv1.1   disabled
TLSv1.2   enabled
TLSv1.3   enabled
komplett
SSLv2     disabled
SSLv3     disabled
TLSv1.0   enabled
TLSv1.1   enabled
TLSv1.2   enabled
TLSv1.3   disabled
vg
SSLv2     disabled
SSLv3     disabled
TLSv1.0   enabled
TLSv1.1   enabled
TLSv1.2   enabled
TLSv1.3   enabled
stortinget
SSLv2     disabled
SSLv3     disabled
TLSv1.0   disabled
TLSv1.1   disabled
TLSv1.2   enabled
TLSv1.3   disabled

hidden ciphers

## HTTP Headers
to mask headers add
ServerTokens Full
ServerSignature Off
to site.conf

1. What would the advantage be of using a certificate form a known CA rather than the
self-signed one we have used in the tutorial.
Authentication
2. What does a certificate attest to about a server?
Server integrity
4. On what basis do we know to trust a server?
it has to be traceable back to the trust root it was signed off of
6. How could a server trust a client ?

#!/usr/bin/bash
name=$1
echo "tlscheck for $name"
echo 'Written by MatSta66614@stud.noroff.no'
sslscan --no-check-certificate --no-ciphersuites --no-fallback --no-renegotiation --no-compression --no-heartbleed --no-check-certificate --no-sigs --no-heartbleed --no-groups $1 | tail -7 | sed '$d'

by receiving the client's certificate during the SSL handshake

