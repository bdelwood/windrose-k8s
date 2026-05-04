# windrose-k8s

[![GitHub License](https://img.shields.io/github/license/bdelwood/windrose-k8s?style=flat-square)](https://github.com/bdelwood/windrose-k8s/blob/master/LICENSE)
[![GitHub release](https://img.shields.io/github/v/release/bdelwood/windrose-k8s?style=flat-square)](https://github.com/bdelwood/windrose-k8s/releases)
[![GitHub Downloads](https://img.shields.io/github/downloads/bdelwood/windrose-k8s/total?style=flat-square)](https://github.com/bdelwood/windrose-k8s/releases)

A Helm chart to deploy a Windrose dedicated server. This chart relies on the [`indifferentbroccoli/windrose-server-docker`](https://github.com/indifferentbroccoli/windrose-server-docker) image.

# How to use

```bash
helm repo add windrose-k8s https://bdelwood.github.io/windrose-k8s/
helm repo update
helm install windrose-server windrose-k8s/windrose-k8s \
  --set gameServer.inviteCode=ABC123 \
  --set gameServer.serverName=windrose-example \
  --set gameServer.password=changeme
```

> [!IMPORTANT]
> `gameServer.inviteCode` is required. It's the 6+ character (`0-9 a-z A-Z`, case-sensitive) identifier players use to find the server through the matchmaking proxy. Share it with players the same way you'd share a server address.

The chart will generate a server password if one is not provided, which can be retrieved with

```bash
kubectl get secrets <secrets-name> -o json | jq '.data | map_values(@base64d)'
```

Alternatively, the password can be supplied in the `values` file or by specifying an existing secret. The secret must have a `server-password` key.

> [!TIP]
> The optional Windrose+ admin dashboard can be enabled with `--set windrosePlus.enabled=true`. Its RCON password is generated into the same secret under the key `windrose-plus-rcon-password`, or supply your own via `windrosePlus.existingSecret`.

By default, the chart will provision a PVC using the default StorageClass. You can also provide an existing PVC, or change the StorageClass.

# Configuration

Full configuration options are detailed in the [Chart readme](/chart/windrose-k8s/README.md).
