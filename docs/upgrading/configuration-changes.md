---
title: Configuration Changes
---

# Configuration Changes

## [Builder](#builder)

The [builder configuration](../configuration/builders) has been simplified.

### Arch

You must specify the architecture(s) you are building for

```yaml
# single arch
builder:
  arch: amd64

# multi arch
builder:
  arch:
    - amd64
    - arm64
```

### Remote builders

Set the remote directly with the remote option. By default it will
only be used if the arch you are building doesn't match the local machine:

```yaml
builder:
  arch: amd64
  remote: ssh://docker@docker-builder
```

You can force Kamal to only use the remote builder, by setting `local: false`.

```yaml
builder:
  arch: amd64
  local: false
  remote: ssh://docker@docker-builder
```

### Driver

Kamal will now always use the docker container (link) driver by default. You can
set the driver yourself to change this:

```yaml
builder:
  driver: docker
```

The docker driver has limited capabilities - it doesn't support build caching or multiarch
images.

## [Traefik -> Proxy](#traefik-to-proxy)

The `traefik` configuration is no longer valid. Instead you can configure kamal-proxy under
`proxy`.

If you were using custom Traefik labels or args, see the proxy configuration for converting those
over.

kamal-proxy supports common requirements such as buffering, max request/response sizes, and forwarding
headers, but it is not the full breadth of everything Traefik can do.

If you don't see something you need, you can raise an issue and we'll look into it, but we don't promise
to support everything.

You might need to run Traefik or another proxy elsewhere in your stack to achieve what you want.

## [Healthchecks](#healthchecks)

The healthcheck section has been removed.

For roles running with a proxy, the healthchecks are performed externally by kamal-proxy, not via
internal Docker healthchecks. You can configure the them under the [proxy section](../../configuration/proxy#healthcheck).

```
proxy:
  healthcheck:
    path: /health
    interval: 2
    timeout: 2
```

For roles that do not run the proxy, you can set a custom docker healthcheck via the [options](../../configuration/roles#custom-role-configuration).

```yaml
servers:
  web:
    ...
  jobs:
    options:
      health-cmd: bin/jobs-healthy
```

For those containers, Kamal will wait for the `healthy` status if they have a healthcheck or
`running` if they don't.

You can set a `readiness_delay` which is used when we see the `running` status. We'll wait
that long and confirm the container is still running before continuing.

There are two timeouts you can set at the root of the config that are used across all roles
whether they use a proxy or not.

```yaml
# how long to wait for new containers to boot
deploy_timeout: 20

# how long to wait for requests to complete before stopping old containers
# Replaces stop_wait_time
drain_timeout: 20

# how long to wait for 'non proxy role' containers without healthchecks to stay in the running state
readiness_delay: 10
```