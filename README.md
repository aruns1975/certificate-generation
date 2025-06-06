# certificate-generation

## Steps to Create a Root Certificate, CSR, and Sign the CSR
<details>
<summary>1. Create a Root Certificate (Root CA)</summary>

```sh
openssl genpkey -algorithm RSA -out rootCA.key
openssl req -x509 -new -key rootCA.key -sha256 -days 3650 -out rootCA.crt -subj "/C=US/ST=State/L=City/O=MyOrg/OU=IT/CN=MyRootCA"
```

* **rootCA.key**: Private key for the root CA.
* **rootCA.crt**: Self-signed root CA certificate.
* **-days 3650**: Valid for 10 years.
</details>

<details>
<summary>2. Generate a Certificate Signing Request (CSR) for an End Entity</summary>
  
```sh
openssl genpkey -algorithm RSA -out server.key
openssl req -new -key server.key -out server.csr -subj "/C=US/ST=State/L=City/O=MyOrg/OU=IT/CN=myserver.com"
```

* **server.key**: Private key for the server.
* **server.csr**: CSR file to be signed.
</details>
<details>
<summary>3. Sign the CSR Using the Root CA</summary>
  
```sh
openssl x509 -req -in server.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out server.crt -days 365 -sha256
```

* **server.crt**: The signed certificate.
* **-CAcreateserial**: Generates a rootCA.srl file to track serial numbers.
</details>

### What Needs to Be Distributed?
<details>
<summary>To the Client or Users:</summary>

* **server.crt** (Signed certificate)
* **rootCA.crt** (CA certificate for verification)
* Any intermediate certificates (if applicable)
</details>
<details>
 <summary>To the Server:</summary>
  
* **server.crt** (Signed certificate)
* **server.key** (Private key, keep it secure)
* **rootCA.crt** (Optional, for mutual TLS)
</details>
<details>
<summary>Verifying the Certificate</summary>
  
```sh
openssl verify -CAfile rootCA.crt server.crt
```

</details>

## Steps to Create a Root Certificate, CSR, Intermediate Certicate and Sign the CSR
<details>
  <summary>1. Create an Intermediate CA</summary>

```sh
openssl genpkey -algorithm RSA -out intermediateCA.key
openssl req -new -key intermediateCA.key -out intermediateCA.csr -subj "/C=US/ST=State/L=City/O=MyOrg/OU=IT/CN=MyIntermediateCA"
openssl x509 -req -in intermediateCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out intermediateCA.crt -days 1825 -sha256

```
</details>

<details>
  <summary>1. Use the Intermediate CA to Sign End-Entity Certificates</summary>

```sh
openssl x509 -req -in server.csr -CA intermediateCA.crt -CAkey intermediateCA.key -CAcreateserial -out server.crt -days 365 -sha256
```
</details>

<details>
  <summary>Distribute the Certificate Chain</summary>

  Provide:
  * server.crt (End-entity certificate)
  * intermediateCA.crt (Intermediate CA certificate)
  * rootCA.crt (Root CA certificate)

**bundle the certificates:
```sh
cat server.crt intermediateCA.crt > server_chain.pem
```
</details>

## Multi-tier Security Architecture
<details>
  <summary>Certificate Heirarchy</summary>
 
  
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
  ```
</details>

