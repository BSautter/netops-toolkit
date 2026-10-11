# Netmiko Quickstart Scripts

Two starter scripts that SSH into a CML Free lab and run
`show ip interface brief`. Run these first to confirm your Python,
Netmiko, and lab connectivity all work before moving on to the larger
pipelines in this repo.

## What You Get

| File | Purpose |
|------|---------|
| `show_interfaces.py` | Connect to Router1 and print `show ip interface brief` |
| `show_interfaces_multi.py` | Loop through all three lab devices, print each hostname and its `show ip interface brief` |
| `NetMiko-Lab.yaml` | CML Free topology for the lab |
| `OUTPUT_EXAMPLES.md` | What both scripts print against a working lab |

## Lab Topology

Three-device CML Free lab: two routers and one L2 switch, bridged to
your host through an External Connector.

```
Router1 (IOL)     E0/0 -- E0/0  Switch1 (IOL-L2)
Router2 (IOL)     E0/0 -- E0/1  Switch1 (IOL-L2)
Switch1 (IOL-L2)  E1/0 -- port  ExternalCon (bridge to host)
```

| Device | Management IP | Platform |
|--------|---------------|----------|
| Router1 | 192.168.1.10 | IOL (iol-xe) |
| Router2 | 192.168.1.11 | IOL (iol-xe) |
| Switch1 | 192.168.1.12 | IOL-L2 (ioll2-xe) |
| ExternalCon | -- | external_connector (System Bridge) |

Credentials: `admin` / `cisco123`

## Prerequisites

- [VMware Workstation Pro](https://support.broadcom.com/group/ecx/downloads) (free for personal use)
- [CML Free](https://developer.cisco.com/docs/modeling-labs/cml-free/) with the included `NetMiko-Lab.yaml` imported
- Python 3
- Netmiko (`pip install netmiko`)
- WSL2 (recommended) or any Linux/macOS terminal
- SSH access from your workstation to all three lab devices

**New to any of these?** Step-by-step setup guides are in the
[getting-started](../../../getting-started/) folder:

| Guide | What it covers |
|-------|---------------|
| [Install WSL2](../../../getting-started/install-wsl2.md) | Windows Subsystem for Linux setup |
| [Install Python 3](../../../getting-started/install-python.md) | Python, pip, and virtual environments |
| [Install Netmiko](../../../getting-started/install-netmiko.md) | The SSH automation library used by these scripts |
| [Install VMware Workstation Pro](../../../getting-started/install-vmware.md) | Free hypervisor for running CML |
| [Install CML Free](../../../getting-started/install-cml-free.md) | Cisco's network simulation platform |

## Quick Start

```bash
# Clone the repo
git clone https://github.com/GTalksTech/netops-toolkit.git
cd netops-toolkit/scripts/netmiko/quickstart-scripts

# Create a virtual environment and install Netmiko
python3 -m venv venv
source venv/bin/activate
pip install netmiko
```

## Lab Setup

1. Import `NetMiko-Lab.yaml` into CML Free
2. Start the lab and wait for all devices to boot
3. Configure the management IPs and SSH yourself. This folder has no
   config file, and the YAML builds the topology only: every device
   node has an empty configuration. In `OUTPUT_EXAMPLES.md` the router
   addresses sit on Ethernet0/0 and the switch address on Vlan1.
4. Verify SSH access:
   ```bash
   ssh admin@192.168.1.10   # Router1
   ssh admin@192.168.1.11   # Router2
   ssh admin@192.168.1.12   # Switch1
   ```

## Running the Scripts

### Single device
```bash
python show_interfaces.py
```

### All three devices
```bash
python show_interfaces_multi.py
```

Compare what you get against [OUTPUT_EXAMPLES.md](OUTPUT_EXAMPLES.md).
If the output matches, your environment is ready for the larger
pipelines.

## Adapting for Your Network

Both scripts use the `cisco_ios` Netmiko device type. To point them at
other devices, edit the `host` value in the `device` dictionary
(`show_interfaces.py`) or the `devices` list
(`show_interfaces_multi.py`).

**Important:** The credentials are hardcoded in both scripts for lab
use. Never use default credentials on production devices.

## License

MIT. See the [LICENSE](../../../LICENSE) file at the root of netops-toolkit.
