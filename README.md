# Setup Workstation

Automated Fedora 44 workstation setup with Ansible. Installs essential development and productivity tools including Google Chrome, Visual Studio Code, Teams for Linux, Terminator, Podman Compose, Git, and ZSH with Oh My Zsh + Powerlevel10k theme. Also configures GNOME desktop preferences (dark theme and window buttons).

## Prerequisites

- Fedora 44 (or compatible)
- Internet connection

## Initial Setup

Commands to prepare the system before running the playbook:

```bash
# Install Ansible and the community.general collection
sudo dnf install -y ansible
ansible-galaxy collection install community.general

# Create passwordless sudo user
echo "jdoe ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/jdoe
```

## Configuration

Two places **must** be edited before running:

**`ansible.cfg`** — set the remote user:
```ini
[defaults]
remote_user = jdoe
```

**`setup.yml`** — set your username and home directory:
```yaml
vars:
  my_user: "jdoe"
  my_home_dir: "/home/jdoe"
```

## How to Run

```bash
ansible-playbook setup.yml
```

## What the Playbook Does

- **System**: repositories (VS Code, Chrome, Teams), DNF packages, SSH Control
- **Shell (ZSH)**: Oh My Zsh, plugins, Powerlevel10k, FiraCode
- **GNOME**: dark theme + window buttons
- **Network**: static IP (conditional, disabled by default)

## Customization

**Package management** — the `my_packages` variable in `setup.yml` contains the packages I consider necessary for my daily workflow. You can remove or add packages to fit your needs, but be aware that modifying this list may cause the playbook to fail. For example, if you add a package that does not exist in the configured repositories, the playbook will error out.

**Static IP configuration** — by default, the network task is disabled (`nic_conf.flag: false`). To configure a static IP on a network interface, change the flag to `true` and fill in the connection details:

| Variable | What it is | Example |
|---|---|---|
| `nic_conf.flag` | Enable static IP | `false` → `true` |
| `nic_conf.my_conn_name` | NetworkManager connection name | `"example-con"` |
| `nic_conf.my_ifname` | Network interface | `"enp3s0"` |
| `nic_conf.my_ip4` | Static IP | `"192.168.1.100/24"` |
| `nic_conf.my_gw4` | Gateway | `"192.168.1.1"` |
| `nic_conf.my_dns4` | DNS | `"8.8.8.8 8.8.4.4"` |

**Other notes:**
- `.zshrc` is copied from the Oh My Zsh template (won't overwrite existing configs thanks to `force: false`)

## Notes

- Runs on `localhost` (no remote inventory needed)
- SSH Control installed via external script
- Static IP disabled by default (`nic_conf.flag: false`)
