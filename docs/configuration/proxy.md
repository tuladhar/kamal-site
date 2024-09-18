---
title: Proxy
---

# Proxy


Kamal uses [kamal-proxy](https://github.com/basecamp/kamal-proxy) to provide
gapless deployments. It runs on ports 80 and 443 and forwards requests to the
application container.

The proxy is configured in the root configuration under `proxy`. These are
options that are set when deploying the application, not when booting the proxy

They are application specific, so are not shared when multiple applications
run on the same proxy.
```yaml
proxy:
```
## [Host](#host)

The hosts that will be used to serve the app. The proxy will only route requests
to this host to your app.

If no hosts are set, then all requests will be forwarded, except for matching
requests for other apps deployed on that server that do have a host set.
```yaml
  host: foo.example.com
```
## [App port](#app-port)

The port the application container is exposed on

Defaults to 80
```yaml
  app_port: 3000
```
## [SSL](#ssl)

kamal-proxy can provide automatic HTTPS for your application via Let's Encrypt.

This requires that we are deploying to a one server and the host option is set.
The host value must point to the server we are deploying to and port 443 must be
open for the Let's Encrypt challenge to succeed.

Defaults to false
```yaml
  ssl: true
```
## [Deploy timeout](#deploy-timeout)

How long to wait for the app to boot when deploying, defaults to 30 seconds
```yaml
  deploy_timeout: 10s
```
## [Response timeout](#response-timeout)

How long to wait for requests to complete before timing out, defaults to 10 seconds
```yaml
  response_timeout: 30s
```
## [Healthcheck](#healthcheck)

When deploying, the proxy will by default hit /up once every second until we hit
the deploy timeout, with a 5 second timeout for each request.

Once the app is up, the proxy will stop hitting the healthcheck endpoint.
```yaml
  healthcheck:
    interval: 3
    path: /health
    timeout: 3
```
## [Buffering](#buffering)

Whether to buffer request and response bodies in the proxy

By default buffering is enabled with a max request body size of 1GB and no limit
for response size.

You can also set the memory limit for buffering, which defaults to 1MB, anything
larger than that is written to disk.
```yaml
  buffering:
    requests: true
    responses: true
    max_request_body: 40_000_000
    max_response_body: 0
    memory: 2_000_000
```
## [Logging](#logging)

Configure request logging for the proxy
You can specify request and response headers to log.
By default, Cache-Control, Last-Modified and User-Agent request headers are logged
```yaml
  logging:
    request_headers:
      - Cache-Control
      - X-Forwarded-Proto
    response_headers:
      - X-Request-ID
      - X-Request-Start
```
## [Forward headers](#forward-headers)

Whether to forward the X-Forwarded-For and X-Forwarded-Proto headers (defaults to false)

If you are behind a trusted proxy, you can set this to true to forward the headers.

By default kamal-proxy will not forward the headers the ssl option is set to true, and
will forward them if it is set to false.
```yaml
  forward_headers: true
```