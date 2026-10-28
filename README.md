# Galtea CLI — Linux packages

Native APT and YUM/DNF package repositories for the [Galtea CLI](https://galtea.ai), served at <https://pkgs.galtea.ai>.

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

## Use

```bash
galtea --version
galtea login
galtea --help
```

Full command reference and tutorials at <https://docs.galtea.ai>.

## Update

The package manager handles upgrades automatically:

```bash
sudo apt update && sudo apt upgrade galtea     # Debian / Ubuntu
sudo dnf upgrade galtea                         # Fedora / RHEL
```

## Install an older version

Both repositories keep every release, so you can install the CLI that matches an older Galtea platform:

```bash
apt list -a galtea                              # Debian / Ubuntu: list the versions
sudo apt install galtea=5.4.0
sudo apt-mark hold galtea                       # keep it at that version on upgrade

dnf list --showduplicates galtea                # Fedora / RHEL: list the versions
sudo dnf install galtea-5.4.0
```

## Uninstall

```bash
sudo apt remove galtea                          # Debian / Ubuntu
sudo dnf remove galtea                          # Fedora / RHEL
```

## Other install channels

| Channel | Install gesture |
|---------|-----------------|
| PyPI | `pip install galtea-cli` |
| Homebrew | `brew install galtea-ai/tap/galtea` |
| Scoop | `scoop bucket add galtea https://github.com/Galtea-AI/scoop-bucket && scoop install galtea` |
| Winget | `winget install galtea` |
| Direct download | [GitHub Releases](https://github.com/Galtea-AI/monorepo/releases) |
