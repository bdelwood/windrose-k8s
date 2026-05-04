# windrose-k8s

![Version: 0.1.0](https://img.shields.io/badge/Version-0.1.0-informational?style=flat-square) ![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square) ![AppVersion: v1.0.5](https://img.shields.io/badge/AppVersion-v1.0.5-informational?style=flat-square)

A basic chart to deploy Windrose dedicated servers.

## Source Code

* <https://github.com/bdelwood/windrose-k8s>

## Requirements

Kubernetes: `>=1.32.0-0`

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Affinity rules for pod scheduling. |
| extraEnv | object | `{}` | Define extra environment variables to pass directly to the container. Any env vars which are set by other values will be overridden. |
| extraVolumeMounts | list | `[]` | Extra volume mounts to add to the container. Pairs with `extraVolumes`. |
| extraVolumes | list | `[]` | Extra volumes to add to the pod. Useful for mounting ConfigMaps with custom UE4SS mod files, override `.ini` snippets, or supplemental Windrose+ configs into `/home/steam/server-files/...`. |
| fullnameOverride | string | `""` | Override the full name of the chart. Default is a combination of release name and chart name. |
| gameServer.directConnection.enabled | bool | `false` | Enable direct (IP:port) connection mode. When false, players join via `inviteCode` through the matchmaking proxy. |
| gameServer.directConnection.proxyAddress | string | `"0.0.0.0"` | Proxy bind address for direct connection mode (`DIRECT_CONNECTION_PROXY_ADDRESS`). |
| gameServer.directConnection.service.nodePortTcp | int | `nil` | Node port for the game server (TCP, for NodePort service type). |
| gameServer.directConnection.service.nodePortUdp | int | `nil` | Node port for the game server (UDP, for NodePort service type). |
| gameServer.directConnection.service.port | int | `7777` | Service port for the game server. Exposed on both TCP and UDP. The image always listens on this port. |
| gameServer.existingSecret | string | `""` | Name of an existing secret for the server password. The secret must contain a `server-password` key. |
| gameServer.generateSettings | bool | `true` | Whether the image should auto-generate `ServerDescription.json` and `WorldDescription.json` from environment variables on first boot. |
| gameServer.inviteCode | string | `""` | Invite code players use to join. **Required.** 6+ chars, `0-9 a-z A-Z`, case-sensitive. Shared with players. |
| gameServer.p2pProxyAddress | string | `"127.0.0.1"` | P2P proxy bind address (`P2P_PROXY_ADDRESS`). Leave at 127.0.0.1 for normal Docker/K8s networking. For LAN play, set to the node's LAN IP and use host networking. |
| gameServer.password | string | `""` | Server password. If one is not provided and an existing secret it not provided, one will be generated. |
| gameServer.pgid | int | `1000` | GID the `steam` user runs as inside the container. See `puid`. |
| gameServer.players | int | `10` | Number of players allowed on the server concurrently. |
| gameServer.puid | int | `1000` | UID the `steam` user runs as inside the container. The image's entrypoint requires this to be set; if either `puid` or `pgid` is empty, the container exits immediately. Should match `podSecurityContext.fsGroup` so the PVC stays writable. |
| gameServer.region | string | `""` | Region for the matchmaking proxy. One of `SEA`, `CIS`, `EU`, or empty string for auto-select. |
| gameServer.serverName | string | `""` | Custom server name shown in the server browser. If unset, defaults to `windrose_<random6>`. |
| gameServer.ue4ssEnabled | bool | `false` | Install the standalone UE4SS runtime. |
| gameServer.update.onStart | bool | `true` | Run SteamCMD update/validate on every container start. |
| gameServer.wineVerbose | bool | `false` | Enable verbose Wine logging. |
| image.pullPolicy | string | `"IfNotPresent"` | Image pull policy |
| image.registry | string | `"docker.io"` | Container registry for the image. |
| image.repository | string | `"indifferentbroccoli/windrose-server-docker"` | Image repository |
| image.tag | string | `""` | Overrides the image tag. Default is the chart appVersion. |
| imagePullSecrets | list | `[]` | Image pull secrets for accessing private container registries. Each entry is an object with a `name` field, e.g. `[{ name: my-registry-creds }]`. |
| nameOverride | string | `""` | Override the name of the chart. Default is the chart name. |
| nodeSelector | object | `{}` | Node selector for pod scheduling. |
| persistence.accessMode | string | `"ReadWriteOncePod"` | Access mode for the persistent volume. |
| persistence.enabled | bool | `true` | Enable or disable persistence. |
| persistence.existingClaim | string | `""` | Name of an existing persistentVolumeClaim. |
| persistence.preventDelete | bool | `true` | Prevent Helm from deleting the PVC. Some storageClasses (such as the local-path-provisioner installed by default by k3s) have reclaimPolicy: Delete. |
| persistence.size | string | `"40Gi"` | Size of the persistent volume. The image needs ~35 GiB for the Wine prefix and game files; default leaves headroom for saves. |
| persistence.storageClassName | string | `""` | Storage class name for the PVC. |
| podAnnotations | object | `{}` | Annotations to add to the pod. |
| podDisruptionBudget.enabled | bool | `false` | Create a PodDisruptionBudget to block voluntary disruptions while the server is running. Recommended when players are connected; admins can `kubectl delete pdb` to unblock maintenance. |
| podDisruptionBudget.maxUnavailable | int | `0` | `maxUnavailable: 0` blocks all voluntary evictions. Tune only if you know what you're doing. |
| podSecurityContext | object | `{"fsGroup":1000}` | Security context for the pod. `fsGroup: 1000` matches the `steam` user inside the image so the PVC is writable. To opt into AppArmor on a host that has it enabled, add:   appArmorProfile:     type: RuntimeDefault |
| probes | object | `{"liveness":{},"readiness":{},"startup":{}}` | Startup, liveness, and readiness probes. |
| probes.liveness | object | `{}` | Liveness probe. |
| probes.readiness | object | `{}` | Readiness probe. Use the dashboard port when Windrose+ is enabled. |
| probes.startup | object | `{}` | Startup probe. Gates liveness/readiness until the server is up. |
| resources.limits | object | `{"cpu":4,"memory":"16Gi"}` | Resource limits (CPU, Memory) for the server. |
| resources.requests | object | `{"cpu":2,"memory":"12Gi"}` | Resource requests (CPU, Memory) for the server. |
| securityContext | object | `{}` | Security context for the pod containers. |
| service.type | string | `"LoadBalancer"` | Service type (e.g., LoadBalancer, ClusterIP, NodePort) |
| serviceAccount.annotations | object | `{}` | Annotations to add to the service account. |
| serviceAccount.create | bool | `false` | Specifies whether a service account should be created. |
| serviceAccount.name | string | `""` | The name of the service account. |
| terminationGracePeriodSeconds | int | `30` | Termination grace period. The image's entrypoint asks for a 30s grace window for clean shutdown. |
| tolerations | object | `{}` | Tolerations for pod scheduling. |
| tz | string | `"UTC"` | Timezone setting for the server. |
| updateStrategy | object | `{"type":"Recreate"}` | Update strategy for deployments. |
| windrosePlus.enabled | bool | `false` | Enable the optional Windrose+ admin dashboard / RCON addon. |
| windrosePlus.existingSecret | string | `""` | Name of an existing secret for the Windrose+ RCON (dashboard) password. The secret must contain a `windrose-plus-rcon-password` key. |
| windrosePlus.rconPassword | string | `""` | Windrose+ RCON (dashboard) password. If one is not provided and Windrose+ is enabled, one will be generated. |
| windrosePlus.service.nodePort | int | `nil` | Node port for the dashboard (for NodePort service type). |
| windrosePlus.service.port | int | `8780` | Service port for the Windrose+ web dashboard (TCP). |
| windrosePlus.version | string | `""` | Pin a specific Windrose+ release tag (e.g. `vX.Y.Z`). Empty string uses the image's default version. |

----------------------------------------------
Autogenerated from chart metadata using [helm-docs v1.14.2](https://github.com/norwoodj/helm-docs/releases/v1.14.2)
