# Mozak infrastructure

Sources: developer-provided inventory and pasted remote diagnostic output received 2026-10-28 (execution date unspecified). No independent remote verification by the agent. Versions, addon state, readiness, routing, certificates, and storage status below are time-sensitive observations. Additional developer-pasted ingress/TLS/storage output supplements the initial inventory; execution date unspecified.

## Cluster and access

- One Kubernetes cluster, running MicroK8s, with one node observed.
- Node: `v220240819124283847`, observed `Ready`; Kubernetes reports no node role labels.
- OS: Debian GNU/Linux 13 (trixie); kernel `6.12.48+deb13-amd64`.
- MicroK8s snap: `v1.32.13`, revision `8702`, tracking `1.32/stable`, classic confinement.
- Kubernetes client/server: `v1.32.13`; bundled Kustomize: `v5.5.0`.
- Container runtime: `containerd://1.6.36`.
- High availability: **no**. Datastore master reported as `127.0.0.1:19001`, no standby nodes. Enabled `ha-cluster` addon does not establish active HA.
- Node INTERNAL-IP field contains a public address; EXTERNAL-IP is unset. This does not establish API, kubelet, or service exposure; firewall and listener configuration remain unverified.
- Development, demonstration, and CI builds only. Production is outside this environment.
- Portainer installed using `microk8s enable portainer`.
- Portainer URL: https://portainer.mozak-it.de
- Developer has SSH access. SSH host, port, user, and authentication configuration are not recorded.
- Current agent mode: manual execution; developer runs supplied commands. Future automation may perform restricted reads only after explicit enablement and server-side enforcement. All remote changes remain manual.

## Addons observed enabled

`portainer`, `cert-manager`, `community`, `dns` (CoreDNS), `ha-cluster`, `helm`, `helm3`, `hostpath-storage`, `ingress`, `metrics-server`, `rbac`, and `storage` (deprecated alias for hostpath-storage).

Observed ingress and storage configuration is recorded below. Actual host volume paths, disk usage, and backup coverage remain unverified.

Relevant addons reported disabled: `argocd`, `metallb`, `traefik`, `nfs`, `openebs`, `mayastor`, `rook-ceph`, `observability`, `prometheus`, and `cis-hardening`. Disabled addons do not rule out separately installed equivalents or other hardening.

## Namespaces

| Namespace | Purpose |
| --- | --- |
| mozak-demo | Stable application version for demonstrations |
| mozak-test | Current application builds deployed automatically by CI |
| portainer | Portainer resources |
| teamcity | TeamCity CI server and agent pods |
| teamcity-operator | Official JetBrains operator managing TeamCity versions |
| nexus | Nexus artifact repository |
| offer | Website application |
| cert-manager | Certificate management; observed issuers/certificates recorded below |
| default | Kubernetes default namespace |
| ingress | ingress-nginx controller DaemonSet |
| kube-node-lease | Kubernetes node leases |
| kube-public | Kubernetes public namespace |
| kube-system | Kubernetes system components |

## Ingress and TLS

- Controller: `ingress/nginx-ingress-microk8s-controller` DaemonSet; image `registry.k8s.io/ingress-nginx/controller:v1.11.5`; desired/current/ready count 1.
- IngressClasses `nginx` and `public` both use `k8s.io/ingress-nginx`. All listed Ingresses use `public` and report address `127.0.0.1`, ports 80/443. This status address does not establish external reachability or host bindings.
- Domains in the supplied output use `our-domain` placeholders. Preserve these as redacted values, not executable hostnames; the separately supplied Portainer URL above remains authoritative.

| Namespace | Ingress names | Hosts as supplied (redacted) |
| --- | --- | --- |
| mozak-demo | awb-backend-ingress; awb-client-ingress | api.demo.our-domain; demo.our-domain |
| mozak-test | awb-backend-ingress; awb-client-ingress | api.test.our-domain; test.our-domain |
| nexus | docker-registry-ingress; nexus-ingress | dockerrepo.our-domain; nexus.our-domain |
| offer | offer-nginx-ingress | offer.our-domain |
| portainer | portainer-ingress; portainer-webhooks-ingress | Both portainer.our-domain |

- ClusterIssuer `letsencrypt-production` observed Ready. Actual ACME endpoint, challenge solver, and certificate issuer references remain unverified; the name alone does not establish them.
- Namespaced Issuer `teamcity-operator/teamcity-operator-selfsigned-issuer` observed Ready.
- All nine listed Certificate resources observed Ready: `api.demo-mozak-tls`, `demo-mozak-tls` in mozak-demo; `api.test-mozak-tls`, `test-mozak-tls` in mozak-test; `docker-registry-tls`, `nexus-tls` in nexus; `offer-mozak-tls` in offer; `portainer-tls` in portainer; `teamcity-operator-serving-cert` in teamcity-operator (Secret name `webhook-server-cert`). Secret contents were not requested.
- No TeamCity namespace Ingress was listed; its access mechanism remains unknown. Certificate readiness does not prove external serving configuration, renewal reliability, or expiry dates.

## Persistent storage

- Default and only listed StorageClass: `microk8s-hostpath`, provisioner `microk8s.io/hostpath`, reclaim policy `Delete`, binding mode `WaitForFirstConsumer`, volume expansion disabled.
- All 17 listed PVs are Bound, use this class, and have reclaim policy `Delete`. Deleting a bound PVC can trigger backing-directory deletion; treat PVC/PV removal as potentially destructive and establish backups first.
- Hostpath is node-local, not replicated storage. RWX labels on exports claims do not establish multi-node shared storage. Requested capacities do not prove enforced disk quotas or available disk space.

| Namespace | Bound claims (requested capacities) |
| --- | --- |
| mozak-demo | awb-logs-pvc (2Gi); awb-storage-pvc (5Gi); diagnostics-data-pvc (100Mi); exports-pvc (10Gi, RWX); postgres-data-pvc (10Gi); postgres-data-timescale-0 (10Gi) |
| mozak-test | awb-logs-pvc (2Gi); awb-storage-pvc (5Gi); diagnostics-data-pvc (100Mi); exports-pvc (10Gi, RWX); postgres-data-pvc (10Gi); postgres-data-timescale-0 (10Gi) |
| nexus | nexus-nexus-repository-manager-data (8Gi) |
| portainer | portainer (10Gi) |
| teamcity | data-postgres-0 (20Gi); teamcity-agent-conf (2Gi); teamcity-data (100Gi) |

All bound claims except the two exports claims report RWO. Both mozak-demo and mozak-test also have `config-data-pvc` and `export-temp-pvc` observed Pending. With `WaitForFirstConsumer`, Pending alone does not establish a fault; consumers/events have not been inspected. No cleanup is authorized by this observation.

## Applications and deployment inventory

Source: additional developer-pasted Helm and controller inventory; execution date unspecified. Helm chart/app versions below are release metadata, not verified running image versions.

| Helm release / namespace | Chart | App version metadata | Status / revision |
| --- | --- | --- | --- |
| nexus / nexus | nexus-repository-manager-64.2.0 | 3.64.0 | deployed / 1 |
| portainer / portainer | portainer-2.33.1 | ce-latest-ee-2.33.1 | deployed / 1 |
| teamcity-operator / teamcity-operator | teamcity-operator-0.0.25 | 0.1.0 | deployed / 1 |

| Namespace | Deployments | StatefulSets |
| --- | --- | --- |
| mozak-demo | awb-backend; awb-client; logrotate | timescale |
| mozak-test | awb-backend; awb-client; logrotate | timescale |
| portainer | portainer | None listed |
| teamcity | teamcity-agent | postgres; teamcity |
| teamcity-operator | teamcity-operator-controller-manager | None listed |
| nexus | nexus-nexus-repository-manager | None listed |
| offer | offer-nginx | None listed |

All listed controllers report 1/1 Ready except `mozak-test/awb-backend`, observed 0/1 Ready, 1 up-to-date and 0 available. Cause has not been investigated; this snapshot is not an ongoing health claim. No DaemonSets were listed in these seven namespaces.

Helm releases establish package inventory but not which human, UI, pipeline, or repository owns future updates. The developer reports that the official JetBrains operator manages TeamCity versions; exact custom resources and controller owner references remain unverified.

## Application delivery pipeline and source of truth

Source: developer clarification in review feedback; configuration has not been independently inspected.

1. TeamCity builds container images and pushes them to Nexus, which serves as the Docker registry.
2. CI generates Kubernetes manifests using Kustomize bases, overlays, and patches. Image tags in the generated manifests select the images to deploy.
3. For a deployment stage, the intended flow includes a webhook call to Portainer, triggering the associated stack to update from the latest configured Git information. The developer describes this trigger as something that should exist; exact wiring and successful delivery remain unverified.
4. Portainer updates the stack from Git. Kubernetes/containerd pulls the referenced images from Nexus according to the manifests and image pull policy; Portainer orchestrates the update rather than itself running the containers.

For applications using this pipeline, durable configuration changes belong in the Kustomize sources or CI generation inputs, not hand edits to generated manifests or live resources. Trace deployment issues through image build/push, manifest generation and Git publication, webhook delivery, Portainer Git synchronization, and Kubernetes rollout. Triggering the webhook or rerunning a deployment pipeline changes state and remains manual.

Source: developer report in follow-up troubleshooting (execution date unspecified): the public stack webhook at `https://portainer.mozak-it.de/api/stacks/webhooks/<REDACTED>` is not reachable externally, while a previous host-local port-forward allowed a webhook call through `http://127.0.0.1`. This is a historical, time-sensitive report, not an independently reproduced observation. Follow-up developer-supplied observations (execution date unspecified): the public UI works; external GET `/api/status` returns HTTP 200 with curl TLS verification result 0 and remote IP `152.53.95.100`; the webhook request returns an nginx HTML `503 Service Temporarily Unavailable`. These narrow investigation toward webhook-specific routing/backend availability but do not identify the failing proxy or backend. Additional developer-pasted routing output (execution date unspecified): both Portainer Ingresses use class `public`, host `portainer.mozak-it.de`, HTTPS backend `portainer:9443`, and TLS Secret `portainer-tls`. The main route is Prefix `/`; the webhook route is Prefix `/api/stacks/webhooks` and additionally enables nginx basic auth using Secret `webhook-basic-auth`. Service `portainer` exposes 9000, 9443, and 30776; one EndpointSlice lists endpoint `10.1.76.227` with these ports (ready condition not supplied). Developer subsequently reports `get secret webhook-basic-auth` returns NotFound in namespace `portainer`. The webhook Ingress therefore references a missing Basic Auth Secret, consistent with nginx's webhook-specific 503. Controller diagnostics remain unverified. Developer subsequently confirms that an external webhook curl request with Basic Auth triggers the deployment. External authenticated webhook delivery is therefore developer-verified; exact remediation steps, TeamCity credential configuration, and completed Kubernetes rollout have not been independently verified. Forwarded resource/port remains unknown.

Developer-selected credential configuration: define `env.PORTAINER_WEBHOOK_API_CREDENTIALS` as a password-type Environment variable on TeamCity's `<Root project>`, inherited by deployment projects, with `username:password` matching nginx's `portainer/webhook-basic-auth` Secret (htpasswd data key `auth`). Do not use an additional agent Kubernetes environment injection as the intended credential source. Local repository inspection shows the legacy agent key file/test still require this key; migration remains pending. Canonical operational documentation: `config/docs/portainer-webhooks.md`; TeamCity parameter inventory: `config/docs/teamcity-versioned-settings.md`. No actual credentials or webhook identifiers belong in these files.

Repositories, branches, generated-manifest paths, stack-to-stage mappings, image tagging/pull policies, and current TeamCity webhook parameter values remain unknown. Keep webhook URLs/tokens outside this skill and chat. Do not assume this application pipeline also owns Nexus, Portainer, TeamCity, or its operator; their maintenance mechanisms need confirmation when relevant.

## Backup policy and coverage

Source: developer statement, not independently verified.

- Portainer: backup created through its UI.
- TeamCity: backup created through its UI.
- mozak-test and mozak-demo databases: developer explicitly considers backups unnecessary. This is a scoped policy decision, not permission for destructive operations and not a policy for the TeamCity database.
- Nexus: no backup available yet; recovery of artifacts/configuration after volume or host loss is not established.
- Portainer and TeamCity backup contents, dates, schedules, off-host locations, encryption/access controls, and restore testing remain unknown. Do not assume a Portainer backup contains application PVC data, or a TeamCity backup includes every database, artifact, and agent configuration.

## Unknowns to discover when relevant

- DNS management, external exposure/firewalls, ingress bindings, certificate issuer references, expiry dates, and ACME solver configuration.
- Host volume paths, available disk space, backup coverage details and restore testing described above; whether Pending PVCs have consumers.
- Application pipeline repository/branch/path and stage-to-stack mappings; webhook wiring and delivery verification; maintenance ownership for infrastructure Helm releases.
- TeamCity operator custom resource types/instances and actual running image versions when relevant.
- SSH endpoint and least-privilege diagnostic access design; no remote access configured by this skill.
