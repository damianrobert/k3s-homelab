# Homelab Kubernetes Cluster
 
Personal [k3s](https://k3s.io) cluster for hosting self-hosted apps and services.
 
Started as a follow-up to working through Kelsey Hightower's *Kubernetes the
Hard Way* — that was for learning how the pieces fit together; this cluster
is the "real" one, built with k3s for day-to-day use.
 
## Architecture
 
Two-node cluster running on Debian 12 (Bookworm) VMs in the office vCenter.
 
| Node        | Role          | OS                    | Hostname     |
|-------------|---------------|-----------------------|--------------|
| VM 1        | control-plane | Debian 12 (Bookworm)  | `k3s-server`   |
| VM 2        | worker        | Debian 12 (Bookworm)  | `k3s-worker1`  |
 
**Networking:** no static IP config on the VMs themselves — addressing is
handled via DHCP reservation per VM, with matching forward and reverse DNS
records.
 
| Hostname              | FQDN                          | IP (DHCP reservation) |
|-----------------------|--------------------------------|------------------------|
| k3s-server            | `k3s-server.kbclab.ro`    | `192.168.182.17`               |
| k3s-worker1           | `k3s-worker1.kbclab.ro`   | `192.168.182.22`               |
 
 
## Repo layout
 
```
k3s-homelab/
├── README.md              # you are here
├── docs/
│   ├── vm-prep.md          # VM sizing + Debian baseline prep checklist
│   └── k3s-install.md      # server/agent install steps, flags used
├── k3s/
│   └── config.yaml         # k3s server config / install flags
├── apps/
│   ├── myapp1/
│   │   └── deployment.yaml
│   └── myapp2/
│       └── helm-values.yaml
└── .gitignore              # kubeconfig, node-token, secrets — never committed
```
 
## Rebuilding from scratch
 
This repo describes the cluster's configuration, not the cluster itself. If a
node needs to be rebuilt:
 
1. Provision a fresh Debian 12 VM in vCenter.
2. Run the baseline OS prep — see [`docs/vm-prep.md`](docs/vm-prep.md).
3. Install k3s (server or agent) — see [`docs/k3s-install.md`](docs/k3s-install.md).
4. Re-apply manifests from `apps/`.
## Secrets
 
Never commit: kubeconfig, the k3s node-token, or any app credentials/API
keys. These are covered by `.gitignore`. If secrets ever need to live in this
repo, use [sops](https://github.com/getsops/sops) or
[sealed-secrets](https://github.com/bitnami-labs/sealed-secrets) rather than
plaintext.
 
## Status
 
- [x] Provision control-plane + worker VMs
- [x] Baseline Debian prep (hostnames, DHCP reservation + DNS, swap off, time sync)
- [ ] Install k3s (server + agent)
- [ ] Deploy first app
- [ ] (optional, later) GitOps via Argo CD / Flux watching this repo

