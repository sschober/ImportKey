# ImportKey

## Overview

### You want

- Enable SSL with Glassfish v3

### You have

- A signed PEM SSL certificate: `<fqdn>.crt`
- A pkcs8 key for your certificate: `<fqdn>.key`

### You need

- A Java Keystore containing your certificate and the full chain
  back to the root certificate

## How to get there


### 1. Convert your key and certificates to DER:

  
    openssl pkcs8 -topk8 -nocrypt -in <fqdn>.key -inform PEM -out <fqdn>.key.der -outform DER
    openssl x509 -in <fqdn>.crt -inform PEM -out <fqdn>.der -outform DER
    openssl x509 -in <intermediate cert n>.crt -inform PEM -out <intermediate cert n>.der -outform DER
    ...
    openssl x509 -in <intermediate cert 0>.crt -inform PEM -out <intermediate cert 0>.der -outform DER
    openssl x509 -in <root cert>.crt -inform PEM -out <rott cert>.der -outform DER


### 2. Create a single file containing the complete chain:

    cat <fqdn>.crt <intermediate cert n>.der ... <intermediate cert 0>.der <root cert>.der > <fqdn>-full-chain.der

### 3. Create a Java Keystore containing your key and the certificate chain

    javac ImportKey.java
    java -cp . ImportKey <fqdn>.key.der <fqdn>-full-chain.der

   This results in a `keystore.jks` file.

### 4. Import newly created key store to the default domain key store

    keytool -importkeystore -srckeystore keystore.jks -destkeystore <domaindir>/config/keystore.jks

