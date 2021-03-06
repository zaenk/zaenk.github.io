# Apache WebSocket Reverse Proxy

## Requirements

- Apache 2.4.5 or later (2.4.9 or later recommended) ([docs][ws tunnel])
- `mod_proxy` module enabled

## Goals

- Route trafic to through Apache to a Web Socket server with reverse proxy
- Terminate TLS connection on Apache, so no additional configuration required on the web socket server

## Configuration

- enable `mod_proxy_wstunnel` module

```apacheconf
LoadModule proxy_wstunnel_module modules/mod_proxy_wstunnel.so
```

- Configure virtual host

```apacheconf
# Optional: redirects all non TLS request to secure ports
<VirtualHost example.com:80>
    ServerName example.com
    Redirect permanent /ws wss://example.com/ws
</VirtualHost>

<VirtualHost example.com:443>
    ServerName example.com:443
    
    # in this case, websocket server (e.g.: Ratchet) listens on localhost
    # this setting also terminates TLS
    ProxyPass /ws ws://localhost:8989
    ProxyPassReverse /ws ws://localhost:8989
    ProxyPreserveHost On
    RequestHeader set X-Forwarded-Proto "wss"

    SSLEngine On
    SSLCertificateFile "/path/to/public.cert.pem"
    SSLCertificateKeyFile "/path/to/private.key.pem"
    <Location /ws>
        SSLRequireSSL
    </Location>
</VirtualHost>
```

## Additional notes

### Ratchet users

I used the following code to start the Rachet server:

```php
<?php

use Ratchet\Server\IoServer;
use Ratchet\Http\HttpServer;
use Ratchet\WebSocket\WsServer;
use MyApp\MyServer;


require __DIR__ . '/vendor/autoload.php';

$server = IoServer::factory(
    new HttpServer(new WsServer(new MyServer())),
    8989
);

$server->run();
```

[ws tunnel]: https://httpd.apache.org/docs/2.4/mod/mod_proxy_wstunnel.html
