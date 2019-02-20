# Spring boot Mutual Authentication

spring boot + Mutual Authentication

## Step

### Generate Server RSA key
```bash
openssl genrsa -aes256 -out serverprivate.key 2048
```
key phrase: server


### Generate x509 certificate and sign with rsa key

```bash
openssl req -x509 -new -nodes -key serverprivate.key -sha256 -days 1024 -out serverCA.crt
```

key phrase: server

**Common Name should be 'localhost'**



### Generate truststore for java

```bash
keytool -import -file serverCA.crt -alias serverCA -keystore truststore.jks
```
keystore password: secret (according to yml)


### Server CA certificate to keystore

```bash
openssl pkcs12 -export -in serverCA.crt -inkey serverprivate.key -certfile serverCA.crt -out keystore.p12
```
Enter pass phrase for serverprivate.key is server

Enter Export Password: secret



### Generate client RSA key

```bash
openssl genrsa -aes256 -out clientprivate.key 2048
```
pass phrase: client


### Generate certificate signing request and sign with client key

```bash
openssl req -new -key clientprivate.key -out client.csr
```
key password: client

**Common Name should be localhost**


### Client sends the CSR to the CA
----
```bash
openssl x509 -req -in client.csr -CA serverCA.crt -CAkey serverprivate.key -CAcreateserial -out client.crt -days 365 -sha256
```
Enter pass phrase for serverprivate.key is server


### Copy keystore, truststore 복사
----
```bash
cp keystore.p12 src/main/resources/
cp truststore.jks src/main/resources/
```