# workspaces-config

A [toha](https://wyrd.foo/docs/toha/) template for the directories a host shares between its workspaces. Workspaces
themselves come from [workspace-template](https://github.com/wyrd-company/workspace-template).

```sh
sudo -u workspaces -i toha templates add gh:wyrd-company/workspaces-config
sudo -u workspaces -i toha apply workspaces-config /workspaces/.shared
```

toha asks for your root CA certificate. To script it, pass an answers file with `--answers -`. Keep that file with
your own host records, not in this repository.

- [docs/host.md](docs/host.md): what to install on the host first, and what this template creates.
- [docs/infrastructure.md](docs/infrastructure.md): the services a host's workspaces depend on.
