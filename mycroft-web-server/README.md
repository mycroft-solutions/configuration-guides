# Web Server

The Nginx HTTP server is used to host static files as well as act as a reverse proxy for services in the Mycroft system.

## Prerequisites

To set up the web server, you will need the newest stable version of the Nginx server installed and running as a service.

## CSR

In order to manage HTTPS connections, a SSL/TLS certificate is needed.
If you have one connected to the domain you are using, place it in `/etc/nginx/ssl/` directory along with the private key.

For testing purposes you can generate a self-signed certificate.
In that case all the services should work correctly, but you will get an information from the browser, that the website is not trusted.

To generate a self-signed certificate in the `/etc/nginx/ssl/` directory you can use **openssl**:

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/<name>.key -out /etc/nginx/ssl/<name>.crt
```

You will be asked about some information regarding your organisation, but the only relevant field in case of a self-signed certificate is `Common Name` which must match the URL you will be using to connect to your website (for example `localhost`, `123.456.78.90` or `somewebsite.net`).

## Configuration

Before you start setting up the MycroftSolutions website, make sure to remove the default Nginx *Welcome* page.

To create Mycroft Web Server configuration create a copy of the [mycroft.conf](./mycroft.conf) file inside the `/etc/nginx/conf.d/` directory.
Next, replace all of the variables marked with diamond brackets (<>) to match your system structure.
For more information read detailed instructions or [example configuration](./example.conf).

### Enforce secure connections

To prevent errors in case of users connecting using an incorrect protocol, we redirect all calls to port 80 (HTTP) to the same address with HTTPS scheme.

```
server {
  listen 80   default_server;
  return 301  https://$host$request_uri;
}
```

### Close invalid connections

If a request does not match any valid virtual server block, it gets closed without a response.
Since we redirect HTTP calls, every invalid call will use HTTPS scheme, so we need a certificate to handle it.

**Do not** use the same certificate for valid requests.
The `nginx.cert` should be a self-signed certificate with default field values, except for `Common Name`, which you should set to the ip address.

```
server {
  listen               443;
  server_name          _;

  ssl_certificate      /etc/nginx/ssl/nginx.crt;
  ssl_certificate_key  /etc/nginx/ssl/nginx.key;

  return               444;
}
```

### Host static files

Currently the frontend gets deployed as a static bundle and gets hosted directly by the NGINX server over HTTPS.
Port 443 is the default target when using the HTTPS protocol, so this will be default location for browsers accessing the server.

```
server {
  listen               443 ssl http2;
  server_name          <server_name>;

  ssl_certificate      /etc/nginx/ssl/<cert_name>.crt;
  ssl_certificate_key  /etc/nginx/ssl/<cert_name>.key;
  ssl_session_cache    builtin:1000 shared:SSL:10m;

  access_log           /var/log/nginx/<log_file>.log;

  location / {
    root       <path_to_static_files>;
    try_files  $uri /index.html;
  }
}
```

#### Variables

* `server_name` - Common Name from the certificate and at the same time the base URL you use to visit the website, for example `localhost`, `123.456.78.90` or `somewebsite.net`
* `cert_name` - name of your main certificate
* `log_file` - used for access log; use a name that will allow you to easily identify the corresponding virtual server
* `path_to_static_files` - full path to the `build/` directory containing the production bundle from `web-app-front` project

### Make a secure connection to an internal service

```
server {
  listen               <port_number> ssl http2;
  server_name          <server_name>;

  ssl_certificate      /etc/nginx/ssl/<cert_name>.crt;
  ssl_certificate_key  /etc/nginx/ssl/<cert_name>.key;
  ssl_session_cache    builtin:1000 shared:SSL:10m;

  access_log           /var/log/nginx/<log_file>.log;

  location / {
    proxy_set_header  Host $host;
    proxy_set_header  X-Real-IP $remote_addr;
    proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header  X-Forwarded-Proto $scheme;

    proxy_pass        http://<service_url>;
  }
}
```

#### Variables

* `port_number` - if the `server_name` is the same for multiple virtual servers, they have to use different ports
* `server_name` - Common Name from the certificate and at the same time the base URL you use to visit the website, for example `localhost`, `123.456.78.90` or `somewebsite.net`
* `cert_name` - name of your main certificate
* `log_file` - used for access log; use a name that will allow you to easily identify the corresponding virtual server
* `service_url` - private url of the service that will process the request

#### Example

Suppose all of your services are running on the same machine with ip `123.456.78.90`, and the backend is using port `5000`.
You can configure a proxy pass with following values for the variables:

* `port_number`: 12350
* `server_name`: 123.456.78.90
* `cert_name`: test
* `log_file`: nginx-12350
* `service_url`: 123.456.78.90:5000

With this configuration, when you make a request to `https://123.456.78.90:12350`, the proxy will reroute it to `http://123.456.78.90:5000` so the backend can process it.
From the user's perspective the response comes from `https://123.456.78.90:12350` and the real service stays hidden.

**Note:** you should never use the `http://123.456.78.90:5000` connection directly from the browser.
All services should be hidden behind a firewall and accessible **ONLY** through the proxy.

## Applying changes

After making changes, validate the structure of your configuration files:

```
nginx -t
```

If there are no issues, you can reload the configuration:

```
nginx -s reload
```

If all the services are set up correctly and running, you can now go to the address of your web server in the browser and the MycroftSolutions website will appear.
