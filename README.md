This repo stores the Kubernetes YAML files for my homelab.

I'm currently running the following services:

 - [FluxCD](https://fluxcd.io/) for GitOps
 - [Tailscale](https://tailscale.com/) for the network layer
 - [Immich](https://immich.app/) for photo hosting
 - [Linkding](https://linkding.link/) for bookmark management

## Setup

### Install Flux

Refer to the [official documentation](https://fluxcd.io/flux/installation/#install-the-flux-cli) to install the latest version of FluxCD for your system.

Current version:

```
$ flux --version
flux version 2.6.2
```

### Tailscale Authentication

Follow these steps to create a Kubernetes Secret called `tailscale-auth`: https://tailscale.com/kb/1185/kubernetes#setup

### Create Postgres Credentials

Choose a DB username and password. Add them to the following file (warning: do not check this into source control):

```
apiVersion: v1
stringData:
  DB_USERNAME: <paste-username-here>
  DB_DATABASE_NAME: immich
  DB_PASSWORD: <paste-password-here>
  username: <paste-username-here>
  password: <paste-password-here>
kind: Secret
metadata:
  name: immich-postgres-user
  namespace: immich
type: kubernetes.io/basic-auth
```

Create the secret:

```
kubectl apply -f postgres-secret.yaml
```

Delete the file.

## Bootstrap Flux

```
flux bootstrap git \
  --url=ssh://git@github.com/rsutton1/kubernetes-homelab \
  --branch=main \
  --private-key-file=<path/to/ssh/private.key> \
  --password=<key-passphrase> \
  --path=kubernetes-homelab
```

Note: Make sure your SSH public key is a deploy key in Github.
