# Apache WebSocket Reverse Proxy

## Requirements

- Apache 2.4.5 or later (2.4.9 or later recommended) ([docs][ws tunnel])
- `mod_proxy` module enabled

## Configuration

- enable `mod_proxy_wstunnel` module

```
LoadModule proxy_wstunnel_module modules/mod_proxy_wstunnel.so
```

- Configure virtual host

```
<VirtualHost local.crm:80>
    ServerName local.crm
    Redirect permanent /ws wss://local.crm/ws
</VirtualHost>

<VirtualHost local.crm:443>
    ServerName local.crm:443
    
    ProxyPass /ws ws://localhost:8989
    ProxyPassReverse /ws ws://localhost:8989
    ProxyPreserveHost On
    RequestHeader set X-Forwarded-Proto "wss"

    SSLEngine On
    SSLCertificateFile "/ssl/local.crm.cert.pem"
    SSLCertificateKeyFile "/ssl/local.crm.key.pem"
    <Location /ws>
        SSLRequireSSL
    </Location>
</VirtualHost>
```

[ws tunnel]: https://httpd.apache.org/docs/2.4/mod/mod_proxy_wstunnel.html