# Self Signed Certificates

## Generate JKS and Certificate using Keytool

Generate JKS and Certificate using java keytool, export to PKCS12 and export certificate and key with OpenSSL.

It is **RECOMMENDED** to set the self singed certificate's `CN` to the domain (`local.crm` in this case) of the server. This is what the Java takes as the defult value for matching certificate for domain names.

```
$ keytool -genkey -alias local.crm -keyalg RSA -keystore local.jks -keysize 2048
$ keytool -importkeystore -srckeystore local.jks -destkeystore local.crm.p12 -deststoretype PKCS12 -srcalias local.crm
$ openssl pkcs12 -in local.crm.p12 -nokeys -out local.crm.cert.pem
$ openssl pkcs12 -in local.crm.p12 -nodes -nocerts -out local.crm.key.pem
```


Certificate information:
- `keytool -keystore local.jks -list`
- `openssl pkcs12 -in local.crm.p12 -info`

## Using JKS in Java Application

Command line arguments: 
```
-Djavax.net.debug=all 
-Djavax.net.ssl.trustStore=local.jks 
-Djavax.net.ssl.trustStorePassword=<keystore_password>
```