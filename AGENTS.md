# workspaces-config

A toha template for `/workspaces/.shared` on a workspace host. See [README.md](README.md).

- This repository is public. Keep it generic: no hostnames, addresses, keys or other details of a particular host.
  Those belong in the host's own records, and answers stay out of this repository.
- `directories` in `template.yml` must match the `/workspaces/.shared` mounts in workspace-template's
  `template/.devcontainer/devcontainer.json`. Change them together.
- The template runs as the unprivileged `workspaces` account and has no hooks. Anything that needs root is a documented
  prerequisite in `docs/host.md`.
