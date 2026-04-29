# Galtea CLI — Linux package repository

This repository hosts the **APT** and **YUM/DNF** package indexes for the [Galtea CLI](https://github.com/Galtea-AI/monorepo/tree/main/cli), served as static files via GitHub Pages at <https://pkgs.galtea.ai>.

The indexes are populated automatically by the [`sync.yml`](.github/workflows/sync.yml) workflow, which reads CLI releases from `Galtea-AI/monorepo` and ingests their `.deb` / `.rpm` artifacts.

## Install

### Debian / Ubuntu

```bash
curl -fsSL https://pkgs.galtea.ai/public.key | sudo tee /etc/apt/keyrings/galtea.asc > /dev/null
echo "deb [signed-by=/etc/apt/keyrings/galtea.asc] https://pkgs.galtea.ai/apt stable main" | sudo tee /etc/apt/sources.list.d/galtea.list
sudo apt update
sudo apt install galtea
```

### Fedora / RHEL / Rocky / AlmaLinux

```bash
sudo rpm --import https://pkgs.galtea.ai/public.key
sudo tee /etc/yum.repos.d/galtea.repo <<'EOF'
[galtea]
name=Galtea CLI
baseurl=https://pkgs.galtea.ai/yum
enabled=1
gpgcheck=1
gpgkey=https://pkgs.galtea.ai/public.key
EOF
sudo dnf install galtea
```

## Layout

```
.
├── apt/                    # reprepro-managed APT index
│   ├── conf/
│   │   ├── distributions   # codename + arches + signing config
│   │   └── options         # reprepro behaviour flags
│   ├── db/                 # reprepro internal state (gitignored locally; committed in CI)
│   ├── dists/              # generated metadata (Release, Packages.gz, signed)
│   └── pool/               # the actual .deb files
├── yum/                    # createrepo_c-managed YUM index
│   ├── repodata/           # generated metadata (repomd.xml + signature)
│   └── *.rpm               # the actual .rpm files
├── public.key              # GPG public key used to sign the indexes
├── index.html              # human-friendly landing page (served by GitHub Pages)
├── CNAME                   # pkgs.galtea.ai
└── .github/workflows/sync.yml
```

## Sync workflow

`sync.yml` triggers:

| Trigger | Use case | Latency |
|---|---|---|
| `repository_dispatch: [new-release]` | Monorepo notifies after a CLI release publishes | ~1 min |
| `workflow_dispatch` (manual) | Full sync, single-release reindex, disaster recovery | On demand |
| `schedule` (daily) | Drift catcher — fills any gaps detected against the monorepo's release list | ≤24 h |

`workflow_dispatch` inputs:
- **`force`** (bool, default `false`) — wipes the index and rebuilds from every CLI release on the monorepo.
- **`version`** (string, optional) — reindexes a single release tag (e.g. `v0.5.2`).
- Both empty → incremental mode (only missing releases are added).

A concurrency group prevents overlapping runs from racing on `git push`.

## Secrets

| Secret | Scope |
|---|---|
| `GPG_PRIVATE_KEY` | Release-signing keypair (private, ASCII-armored) |
| `GPG_PASSPHRASE` | Passphrase for the above |
| `MONOREPO_READ_TOKEN` | Fine-grained PAT — `contents: read` on `Galtea-AI/monorepo` |

## Bootstrap

See [`cli/BOOTSTRAP.md`](https://github.com/Galtea-AI/monorepo/blob/main/cli/BOOTSTRAP.md) in the monorepo for one-time setup steps (GPG key generation, secret configuration, Pages, CNAME).
