# certificate-generation

## Steps to Create a Root Certificate, CSR, and Sign the CSR
<details>
<summary> 
 **1. Create a Root Certificate (Root CA)** 
</summary>

```sh
openssl genpkey -algorithm RSA -out rootCA.key
openssl req -x509 -new -key rootCA.key -sha256 -days 3650 -out rootCA.crt -subj "/C=US/ST=State/L=City/O=MyOrg/OU=IT/CN=MyRootCA"
```

* **rootCA.key**: Private key for the root CA.
* **rootCA.crt**: Self-signed root CA certificate.
* **-days 3650**: Valid for 10 years.
</details>
<details>
<summary>
 
 **2. Generate a Certificate Signing Request (CSR) for an End Entity**
</summary>
  
```sh
openssl genpkey -algorithm RSA -out server.key
openssl req -new -key server.key -out server.csr -subj "/C=US/ST=State/L=City/O=MyOrg/OU=IT/CN=myserver.com"
```

* **server.key**: Private key for the server.
* **server.csr**: CSR file to be signed.
</details>
<details>
<summary>
 
**3. . Sign the CSR Using the Root CA**
</summary>
  
```sh
openssl x509 -req -in server.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out server.crt -days 365 -sha256
```

* **server.crt**: The signed certificate.
* **-CAcreateserial**: Generates a rootCA.srl file to track serial numbers.
</details>

### What Needs to Be Distributed?
#### To the Client or Users:

* **server.crt** (Signed certificate)
* **rootCA.crt** (CA certificate for verification)
* Any intermediate certificates (if applicable)

#### To the Server:

* **server.crt** (Signed certificate)
* **server.key** (Private key, keep it secure)
* **rootCA.crt** (Optional, for mutual TLS)

### Verifying the Certificate
```sh
openssl verify -CAfile rootCA.crt server.crt
```

## Multi-tier Security Architecture
```
Root CA (Offline)  
  │  
  ├── Intermediate CA 1 (Online)  
  │      ├── Web Server Certificates  
  │      ├── API Server Certificates  
  │      └── Client Certificates  
  │  
  ├── Intermediate CA 2 (Online)  
  │      ├── IoT Device Certificates  
  │      ├── VPN Certificates  
  │      └── Email Signing Certificates  
