# certificate-generation

**1. Create a Root Certificate (Root CA)**

```sh
openssl genpkey -algorithm RSA -out rootCA.key
openssl req -x509 -new -key rootCA.key -sha256 -days 3650 -out rootCA.crt -subj "/C=US/ST=State/L=City/O=MyOrg/OU=IT/CN=MyRootCA"
```

* rootCA.key: Private key for the root CA.
* rootCA.crt: Self-signed root CA certificate.
* -days 3650: Valid for 10 years.
