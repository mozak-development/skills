# Initial discovery — developer runs manually

Run these in your existing **remote SSH shell**, not on your Mac. They request configuration summaries and do not intentionally change cluster or host configuration. They may produce access/audit logs and local client caches. Review output before sharing; redact internal addresses or domains if sensitive. No credentials or Secret contents are requested.

Run one batch at a time. If access is denied, share the error rather than changing permissions. If your normal MicroK8s commands require sudo, use your existing administrator procedure manually; this is not a proposal to grant an agent sudo.

## 1. Host and cluster

```bash
# READ-ONLY — host OS, MicroK8s release, enabled addons, node count/version.
uname -sr
lsb_release -ds
snap list microk8s
microk8s status
microk8s kubectl version
microk8s kubectl get nodes -o wide
```

Some distributions do not install `lsb_release`; skip it if unavailable. `microk8s status` reports current status; omit `--wait-ready` to avoid an indefinite wait. Hostnames and IPs in node output may be sensitive.

## 2. Network and TLS

```bash
# READ-ONLY — controller inventory, ingress routing, service exposure.
microk8s kubectl get ingressclasses
microk8s kubectl -n ingress get deployments,daemonsets,services -o wide
microk8s kubectl get ingresses -A
microk8s kubectl get services -A
# cert-manager presence/configuration summaries; API may be unavailable.
microk8s kubectl get clusterissuers.cert-manager.io
microk8s kubectl get issuers.cert-manager.io,certificates.cert-manager.io -A
```

These establish actual ingress classes and TLS resource status. They do not establish external DNS ownership, firewall configuration, or certificate renewal reliability. Ask the developer about those only when relevant. Avoid dumping issuer YAML, which may contain sensitive provider configuration.

## 3. Persistent storage

```bash
# READ-ONLY — provisioners, default class, reclaim policy, volume claims.
microk8s kubectl get storageclasses
microk8s kubectl get persistentvolumes
microk8s kubectl get persistentvolumeclaims -A
```

If a volume needs investigation, request selected fields for that specific volume rather than all volume YAML. Backups and restore testing cannot be inferred from bound PVCs: ask which backup tool exists, what it covers, where backups live, and when restoration was last tested.

## 4. Workload inventory and deployment ownership

```bash
# READ-ONLY — known application and tooling namespaces.
for ns in mozak-demo mozak-test portainer teamcity teamcity-operator nexus offer; do
  printf '\n=== %s ===\n' "$ns"
  microk8s kubectl -n "$ns" get deployments,statefulsets,daemonsets,pods
 done
# READ-ONLY — discover operator resource types without reading their contents.
microk8s kubectl get customresourcedefinitions -o name
```

Workload names alone do not prove deployment ownership. Ask how each workload is deployed/updated. When needed, inspect selected owner references and Helm/management labels on a specific workload. Helm release listings are optional after determining whether the installation uses host Helm or `microk8s helm3`; do not enable an addon to inspect it. Avoid full release values/manifests by default because they can contain secrets.

Record confirmed persistent facts in `environment.md`; include the observation date for versions and runtime configuration. Keep raw output outside the skill. An unavailable API or command is an observation, not permission to install or enable it.
