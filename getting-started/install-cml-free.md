# Installing Cisco CML Free

Cisco Modeling Labs (CML) Free is Cisco's personal-use network simulation platform. It runs as a VM on your local machine and supports up to 5 nodes per lab. That is enough for every topology in this repo.

If you already have CML Free running, skip to [Import the Lab Topology](#step-5-import-the-lab-topology).

---

## What You Need

- A PC with at least 8 GB RAM and virtualization enabled in BIOS (Intel VT-x or AMD-V)
- VMware Workstation Pro (free for personal use as of late 2024)
- A free Cisco account

---

## Step 1: Create a Cisco Account

If you already have a Cisco account (CCO ID), skip this step.

Go to [developer.cisco.com](https://developer.cisco.com) and click **Log In** or **Sign Up**. Follow the prompts to create a free account. This is the same account used for Cisco documentation, software downloads, and DevNet.

---

## Step 2: Download CML Free

Navigate to the CML Free page:

```
https://developer.cisco.com/docs/modeling-labs/cml-free/
```

Log in with your Cisco account and download the OVA file. It is roughly 20 GB, so give it time.

---

## Step 3: Import the OVA into VMware Workstation

1. Open VMware Workstation Pro.
2. Go to **File > Open** and select the downloaded `.ova` file.
3. Accept the defaults for VM name and storage location (or change them if you prefer).
4. Click **Import** and wait for it to finish.

Before booting the VM, review the VM settings. CML recommends at least 2 vCPUs and 8 GB of RAM for the VM. If your machine has 16 GB+ total, allocating 8 GB to CML works well.

---

## Step 4: Boot CML and Complete Initial Setup

Power on the VM. The first boot takes a few minutes while CML initializes.

You will be prompted to:

- Set an admin password (this is for the CML web UI and API)
- Configure networking (accept the defaults for DHCP unless you need a static IP)

Once setup completes, CML will display its IP address in the console. Note this down.

---

## Step 5: Access the CML Web UI

Open a browser and go to:

```
https://<vm-ip>
```

Accept the self-signed certificate warning and log in with username `admin` and the password you set during setup.

---

## Step 6: Import the Lab Topology

1. In the CML web UI, click **Import** (or use the menu under Labs).
2. Select the `NetMiko-Lab.yaml` file from the folder of the script you plan to run. Each script folder under `scripts/netmiko/` ships its own copy, and they are not all the same lab:
   - `scripts/netmiko/quickstart-scripts/NetMiko-Lab.yaml`: Router1, Router2, and Switch1
   - `scripts/netmiko/ai-network-documentation/NetMiko-Lab.yaml` and `scripts/netmiko/api-automation-pipeline/NetMiko-Lab.yaml`: core-rtr-01, edge-rtr-01, and access-sw-01
3. The topology will appear in your lab list.

The YAML files build the topology only. The device nodes carry no configuration, so none of them sets a management IP. Where the IPs come from is covered in Verifying Connectivity below.

---

## Step 7: Start the Lab

Open the imported lab and click **Start Lab**. All nodes need to fully boot before you can connect to them. This typically takes 2 to 5 minutes depending on your hardware.

Wait until every node shows a green "BOOTED" status in the CML UI before proceeding.

---

## Setting Up the External Connector

CML uses an External Connector node to bridge lab devices onto your host network. This is what lets you SSH from your machine (or WSL2) directly into lab devices.

In the lab topology, the External Connector is already configured in **bridge mode**. For it to work, you need to make sure VMware is bridging to the correct network adapter on your host.

### Selecting the Right VMware Network Adapter

1. In VMware Workstation, go to **Edit > Virtual Network Editor**.
2. Find the bridged network (usually VMnet0).
3. Set it to bridge to your active physical adapter (the one connected to your LAN). Do not use "Automatic" if you have multiple adapters, as it may pick the wrong one.
4. If you are on Wi-Fi, bridge to your wireless adapter. If you are on Ethernet, bridge to your wired NIC.

After changing this, reboot the CML VM or restart the External Connector node inside CML so it picks up the new network path.

---

## Verifying Connectivity

Once the lab is running and the External Connector is bridged correctly, test SSH access from your terminal (WSL2 or any SSH client):

```bash
ssh admin@192.168.1.250
```

Use the lab credentials (password: `cisco123`). The IP depends on which lab you imported:

- **ai-network-documentation** and **api-automation-pipeline**: paste each device's block from that folder's `lab-configurations.cfg` into its console first. That sets the IPs listed in the folder's README (core-rtr-01 at 192.168.1.250, access-sw-01 at 192.168.1.251).
- **quickstart-scripts**: this folder has no config file. Its [README](../scripts/netmiko/quickstart-scripts/README.md) lists the addresses the scripts expect (Router1 at 192.168.1.10, Router2 at 192.168.1.11, Switch1 at 192.168.1.12), so configure those addresses and SSH on the devices yourself, then test with `ssh admin@192.168.1.10`.

If the connection succeeds, your lab is ready.

---

## Common Issues

**CML VM does not get an IP address.**
Check that VMware networking is set to Bridged (not NAT or Host-Only). Verify the bridge is pointed at your active adapter in the Virtual Network Editor.

**Cannot SSH to lab devices.**
Make sure all nodes show "BOOTED" in the CML UI. Devices that are still initializing will not accept SSH connections. Also confirm the External Connector is bridged to the right adapter.

**Devices boot but External Connector shows no link.**
This usually means the VMware bridge adapter selection is wrong. Open the Virtual Network Editor and explicitly select your active NIC instead of "Automatic."

**SSH connection refused or timeout.**
Double-check the device management IP against the README in the folder you imported the lab from, and confirm the device's config was actually applied. If the IP does not respond to ping, the issue is Layer 2/3 between your host and the CML bridge. Verify the bridge adapter and check for firewall rules on your host that might block traffic to the lab subnet.

---

## You're Ready

With CML Free running and lab connectivity verified, all prereqs are in place. Head back to the [main README](../README.md) for the Quick Start workflow.
