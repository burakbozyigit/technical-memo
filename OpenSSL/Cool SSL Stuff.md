# SSL Configuration in General
## SSL Config File with Alternative Names


**ssl.cfg**
```
[ req ]
prompt = no
default_bits = 4096
default_md = sha256
req_extensions  = req_ext
x509_extensions = req_ext
distinguished_name = dn
[ dn ]
CN = name0.domain.com
OU = Infrastructure
O = My lovely company
L = Uskudar
ST = Istanbul
C=TR
[ req_ext ]
subjectAltName = @alt_names
[ alt_names ]
DNS.0 = name1.domain.com
DNS.1 = name2.domain.com
```


## Creation of SSL Certificate

```openssl req -new -x509 -sha256 -newkey rsa:2048 -keyout priv.key -out cert.crt -nodes -set_serial 0 -days 3650 -config ssl.cfg```


## Create PKCS12 Certificate

```
openssl pkcs12 -export -out pkcs12.pfx -inkey priv.key -in cert.crt # Create pfx file
openssl pkcs12 -info -in pkcs12.pfx # Verify
```


## Just Create a Certificate Bundle
`cat priv.key cert.crt> bundle.pem`

More: <https://www.digicert.com/ssl-support/pem-ssl-creation.htm>


## Turn PKCS12 private key to DER/CRT
`openssl pkcs12 -in path.p12 -out newfile.key.pem -nocerts -nodes`


## SSL Pinning
`openssl rsa -in private.key -outform der -pubout | openssl dgst -sha256 -binary | openssl enc -base64`
