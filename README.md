# web-app-proxy

This is an nginx configuration using `docker-compose` that provides proxying of remote services that are protected by client web credentials to locally hosted web applications. This avoids the need to allow CORS via setting Access-Control-Allow-Origin in response headers in these services.

It was originally created for Angular web apps using web-cred-controlled services on gcp, but it can be adapted.

## Usage

1. Clone and `cd` to repo folder 
1. Extract your web creds from your p12 file, if needed (see below)
    OR
1. Provide your own `client.pem` and `client.key`
1. Run `docker-compose up -d`
1. Serve web app with `ng serve --disable-host-check`
1. Browse to http://localhost:4202

`docker-compose down` when finished

## Extracting web creds from a .p12

### Extract cert chain:
```
openssl pkcs12 -in "Your.p12" -nokeys -out client.pem -nodes
```

### Extract private key:
```
openssl pkcs12 -in "Your.p12" -nocerts -out client.key -nodes
```

## What's happening

A local nginx proxy is running in a docker network, exposing port 4202 to your host machine. HTTP and Web Socket requests are being proxied to the appropriate locations:

* http://localhost:4202/ => http://localhost:4200/ where angular is serving the app

* ws://localhost:4202/sockjs-node/ => ws://localhost:4200/sockjs-node/ 
