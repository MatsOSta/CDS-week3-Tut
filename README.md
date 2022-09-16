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
