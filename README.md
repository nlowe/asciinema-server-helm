# asciinema-server-helm

A helm chart for [asciinema](https://github.com/asciinema/asciinema-server).

> **NOTE**: This chart was quickly thrown together to get an internal deployment
> of Asciinema up and running. There are a *lot* of improvements that could be
> made that I do not currently have time to make. As such, this chart makes a
> lot of assumptions about your environment that may not be suitable for all
> environments.

## Dependencies

This chart depends on `bitnami/postgresql` and `bitnami/redis`:

```bash
helm repo add bitnami-stable https://charts.bitnami.com
helm repo update
helm dep up .
```

## Deploying

Clone this repo, deploy dependencies, then `helm upgrade`:

```bash
helm upgrade --install asciinema . --namespace asciinema --wait
```

## Values

| Name | Description | Default |
| --- | --- | --- |
| `image.repository` | The asciiname-server image to use | `asciinema/asciinema-server` |
| `image.tag` | The tag to use |  `latest` |
| `image.pullPolicy` | The PullPolicy to use | `Always` |
| `server.secretKeyBase` | The Secret Key for signing cookies | `{{ randAlphaNum 64 | b64enc | quote }}`|
| `server.archiveAnonymousAfterDays` | How many days to keep anonymous recordings for | `7` |
| `persistence.enabled` | Enable persistence | `true` |
| `persistence.existingClaim` | Use an existing PVC. If not set, defaults to a new 25Gi volume | Not Set |
| `email.image.repository` | The image to use for the SMTP Relay | `namshi/smtp` |
| `email.image.tag` | The tag to use for the SMTP Relay Image | `latest` |
| `email.image.pullPolicy` | The PullPolicy to use for the SMTP Relay Image | `Always` |
| `email.relay.host` | The SMTP Server to relay emails to | `relay.mydomain.net` |
| `email.relay.cidr` | The network that the relay host resides on | `10.0.0.0/8` |
| `email.relay.aliases` | Email domains to send invites for | `[*.mydomain.net]` |
| `email.from` | The `From` address to use for emails | `admin@asciinema.mydomain.net` |
| `email.replyTo` | The `Reply To` address to use for emails | `admin@asciinema.mydomain.net` |
| `email.resources` | Resource Limits | See [values.yaml](./values.yaml) |
| `postgresql.*` | Values for Postgres | See [`bitnami/postgresql`](https://hub.helm.sh/charts/bitnami/postgresql/3.14.3) |
| `redis.*` | Values for Redis | See [`bitnami/redis`](https://hub.helm.sh/charts/bitnami/redis/6.4.2) |
| `service.type` | The Service Type to use | `ClusterIP` |
| `service.port` | The Service Port to use | `80` |
| `ingress.enabled` | Enable Ingress | `true` |
| `ingress.annotations` | Ingres Annotations | `{}` |
| `ingress.path` | The Ingress path to listen on | `/` |
| `ingress.hosts` | Hostnames to listen on | `[asciinema.mydomain.net]` |
| `ingress.tls` | Ingress TLS Settings | `{}` |
| `resources` | Resource Limits | See [values.yaml](./values.yaml) |
| `nodeSelector` | NodeSelector for pods | `{}` |
| `tolerations` | Tolerations for pods | `[]` |
| `affinity` | Affinity for pods | `{}` |

## Upgrading

The Server pod will automatically run database migrations in an init container,
they do not need to be run manually.
