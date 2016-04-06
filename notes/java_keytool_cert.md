# Self Signed Certificates

## Generate JKS and Certificate using Keytool

Generate JKS and Certificate using java keytool, export to PKCS12 and export certificate and key with OpenSSL.

It is **RECOMMENDED** to set the self singed certificate's `CN` to the domain (`local.test` in this case) of the server. This is what the Java takes as the defult value for matching certificate for domain names.

```
$ keytool -genkey -alias local.test -keyalg RSA -keystore local.jks -keysize 2048
$ keytool -importkeystore -srckeystore local.jks -destkeystore local.test.p12 -deststoretype PKCS12 -srcalias local.test
$ openssl pkcs12 -in local.test.p12 -nokeys -out local.test.cert.pem
$ openssl pkcs12 -in local.test.p12 -nodes -nocerts -out local.test.key.pem
```

### Adding public certificate to Java Kestore

```
$ keytool -import -alias local.test -file local.test.cert.pem -keystore local.jks
```

### Certificate information

- `keytool -keystore local.jks -list`
- `openssl pkcs12 -in local.test.p12 -info`

## Using JKS in Java Application

Command line arguments: 
```
$ java -jar app.jar \
    -Djavax.net.debug=all \
    -Djavax.net.ssl.trustStore=local.jks \
    -Djavax.net.ssl.trustStorePassword=<keystore_password>
```
