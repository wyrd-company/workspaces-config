# Infrastructure

Workspaces depend on these services. They do not have to run on the workspace host, and one set can serve several hosts.
Names below are what workspace-template expects today. Where the template sets a default, its interview can override it.

## Network

- **Overlay network:** an attachable Docker overlay network named `homelab`, joined by every workspace host and by the
  ingress. Containers are reached over it and publish no ports. workspace-template hard-codes this name in `runArgs`.
- **DNS:** names for your internal services, and for each workspace's `t3.`, `workflows.` and `*.` hosts, must resolve to
  the ingress from the machines that use them.

## Ingress

A [caddy-docker-proxy](https://github.com/lucaslorentz/caddy-docker-proxy) reverse proxy that reads the `caddy_*` labels
of every workspace host's containers, such as through a read-only Docker socket proxy on each host. It must provide:

- A snippet named `openbao_tls` that issues certificates from your internal CA. Workspaces import it.
- On-demand TLS permission. Each workspace's wildcard route uses `tls on_demand`, and Caddy refuses its whole
  configuration without an `ask` setting (`on_demand_tls { ask <url> }`). That failure blocks updates for every site on
  that proxy, not just the workspace. The endpoint decides which names may get a certificate, so restrict it as far as
  your network requires.

## Secrets

[OpenBao](https://openbao.org) and [Wyrwood](https://wyrd.foo/docs/wyrwood/). The container holds no token, key or secret
file: the host's Wyrwood agent gives it `vault.sock` and `ssh-agent.sock`. In the namespace Wyrwood uses:

- **KV (`kv/`):** workspace secrets, each workspace's model gateway key, and operator details. See workspace-dotfiles'
  `docs/vault.md` for the paths.
- **Transit (`transit/`):** the private keys of your GitHub Apps and your operators' SSH keys. Keys are imported and never
  leave OpenBao.
- **Wyrwood policies:** one shared by every workspace, and one per workspace for its gateway key, so no workspace can read
  another's. A policy grants signing with `path "transit/sign/<key>" { capabilities = ["update"] }`.
- **PKI:** an intermediate CA with ACME enabled, for the ingress.

## GitHub

GitHub Apps installed on your organization, each with a Transit key: one for automation, which clones and pushes
workspace branches and so needs contents write, and one per agent harness.

## Model gateway

[Xolotl](https://github.com/wyrd-company/xolotl), an API gateway for coding agents. Each workspace has its own client key.

## Optional

- **NATS:** trowel, in every workspace container, connects to it. Until it resolves, trowel restarts in a loop.
- **OTLP receiver:** where the container's OpenTelemetry collector can export.
