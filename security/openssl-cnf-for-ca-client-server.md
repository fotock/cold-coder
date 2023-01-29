```conf
HOME			= .
openssl_conf = openssl_init
config_diagnostics = 1
oid_section = new_oids

[openssl_init]
providers = provider_sect
ssl_conf = ssl_module

[ ssl_module ]
system_default = crypto_policy

[ crypto_policy ]

.include = /etc/crypto-policies/back-ends/opensslcnf.config

[ ca ]
default_ca	= CA_default		# The default ca section

[ CA_default ]

dir		= .		# Where everything is kept
certs		= $dir/certs		# Where the issued certs are kept
crl_dir		= $dir/crl		# Where the issued crl are kept
database	= $dir/index.txt	# database index file.
#unique_subject	= no			# Set to 'no' to allow creation of
					# several certs with same subject.
new_certs_dir	= $dir/newcerts		# default place for new certs.

certificate	= $dir/cacert.pem 	# The CA certificate
serial		= $dir/serial 		# The current serial number
crlnumber	= $dir/crlnumber	# the current crl number
					# must be commented out to leave a V1 CRL
crl		= $dir/crl.pem 		# The current CRL
private_key	= $dir/private/cakey.pem # The private key

x509_extensions	= usr_cert		# The extensions to add to the cert

# Comment out the following two lines for the "traditional"
# (and highly broken) format.
name_opt 	= ca_default		# Subject Name options
cert_opt 	= ca_default		# Certificate field options

default_days	= 365			# how long to certify for
default_crl_days= 30			# how long before next CRL
default_md	= sha256		# use SHA-256 by default
preserve	= no			# keep passed DN ordering

# A few difference way of specifying how similar the request should look
# For type CA, the listed attributes must be the same, and the optional
# and supplied fields are just that :-)
policy		= policy_match

# For the CA policy
[ policy_match ]
countryName		= match
stateOrProvinceName	= match
organizationName	= match
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

# For the 'anything' policy
# At this point in time, you must list all acceptable 'object'
# types.
[ policy_anything ]
countryName		= optional
stateOrProvinceName	= optional
localityName		= optional
organizationName	= optional
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

####################################################################
[ req ]
default_bits		= 4096
default_md		= sha256
default_keyfile 	= privkey.pem
distinguished_name	= req_distinguished_name
attributes		= req_attributes
x509_extensions	= v3_ca	# The extensions to add to the self signed cert

# WARNING: ancient versions of Netscape crash on BMPStrings or UTF8Strings.
string_mask = utf8only

# req_extensions = v3_req # The extensions to add to a certificate request

[ req_distinguished_name ]
countryName			= Country Name (2 letter code)
countryName_default		= CN
countryName_min			= 2
countryName_max			= 2

stateOrProvinceName		= State or Province Name (full name)
stateOrProvinceName_default	= Beijing

localityName			= Locality Name (eg, city)
localityName_default		= Beijing

0.organizationName		= Organization Name (eg, company)
0.organizationName_default	= Y1Tech

organizationalUnitName		= Organizational Unit Name (eg, section)
#organizationalUnitName_default	=

commonName			= Common Name (eg, your name or your server\'s hostname)
commonName_max			= 64
emailAddress			= Email Address
emailAddress_max		= 64

[ req_attributes ]
challengePassword		= A challenge password
challengePassword_min		= 4
challengePassword_max		= 20

unstructuredName		= An optional company name

[ usr_cert ]
basicConstraints=CA:FALSE
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

[ v3_req ]
basicConstraints = CA:FALSE
nsCertType = client, email
nsComment = "OpenSSL Generated Client Certificate"
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
keyUsage = critical, nonRepudiation, digitalSignature, keyEncipherment
extendedKeyUsage = clientAuth, emailProtection

[ v3_ca ]
# Extensions for a typical CA
# PKIX recommendation.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid:always,issuer
basicConstraints = critical,CA:true

[ v3_intermediate_ca ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:0
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[insta] # CMP using Insta Demo CA
# Message transfer
server = pki.certificate.fi:8700
# proxy = # set this as far as needed, e.g., http://192.168.1.1:8080
# tls_use = 0
path = pkix/

# Server authentication
recipient = "/C=FI/O=Insta Demo/CN=Insta Demo CA" # or set srvcert or issuer
ignore_keyusage = 1 # potentially needed quirk
unprotected_errors = 1 # potentially needed quirk
extracertsout = insta.extracerts.pem

# Client authentication
ref = 3078 # user identification
secret = pass:insta # can be used for both client and server side

# Generic message options
cmd = ir # default operation, can be overridden on cmd line with, e.g., kur

# Certificate enrollment
subject = "/CN=openssl-cmp-test"
newkey = insta.priv.pem
out_trusted = insta.ca.crt
certout = insta.cert.pem
```
