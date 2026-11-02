# Host

A host runs workspace devcontainers as Docker containers. Everything here runs on that host.

## Prerequisites

These need root, so this template does not do them:

1. Install Docker, and join the host to the overlay network described in [infrastructure.md](infrastructure.md).
2. Install [dcb](https://wyrd.foo/docs/dcb/), [toha](https://wyrd.foo/docs/toha/) 0.4.1 or later, git and jq. On Debian
   and Ubuntu, `iproute2` also ships a `dcb` in `/usr/sbin`, so link ours first on `PATH`:
   `ln -s /usr/bin/dcb /usr/local/bin/dcb`.
3. Install and enroll the Wyrwood agent.
4. Create the `workspaces` account in the `docker` group, with uid and gid 1000 to match the container's `vscode` user,
   and add it to the access group you will use in Wyrwood bindings.
5. Create the workspace root: `install -d -o workspaces -g workspaces -m 0755 /workspaces`.

## What this template creates

Apply it to `/workspaces/.shared`, as `workspaces`:

| Path | Purpose |
| --- | --- |
| `ca-certificates/homelab-root.crt` | Your root CA, mounted read-only into every container. workspace-template expects this filename. |
| `caches/` | Package and tool caches shared by every workspace on the host, so a rebuild downloads less. |
| `mnemonic-vault/` | The user-level memory vault. It starts empty. |

`caches/go-pkg` and `caches/cargo` are only mounted by workspaces that select the Go or Rust SDK.

To use a vault, once the host has git credentials, clone into the empty directory:
`git clone -b main <url> /workspaces/.shared/mnemonic-vault`.

## What this template does not create

Each workspace also needs resources outside this directory. See workspace-template's `docs/devcontainers.md`
for the first-start sequence, which creates the Wyrwood directory and binds the container.
