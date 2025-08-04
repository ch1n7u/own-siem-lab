## Lab Environment

| VM              | CPU | RAM  | Network         | OS                  |
| --------------- | --- | ---- | --------------- | ------------------- |
| `SIEM-Manager`  | 2   | 4 GB | Bridged adapter | Ubuntu Server 22.04 |
| `Windows-Agent` | —   | —    | Same network    | Windows 11 Home     |

---

## Prerequisites

- Hypervisor (VirtualBox, VMware, etc.)
- Internet access for package downloads
- Static IP assigned to Ubuntu Server (e.g. `192.168.1.50`)

---

## 1. Install Ubuntu Server and GUI

1. **Deploy Ubuntu Server 22.04**

   - Assign at least **2 vCPUs** and **4 GB RAM**.
   - Configure bridged networking with a static IP.

2. **Add GUI (Xfce)**

   ```bash
   sudo apt update && sudo apt install xubuntu-core -y
   ```

3. **Enable a desktop session**

   ```bash
   sudo reboot
   ```

---

## 2. Install Wazuh Manager & Indexer

1. **Import Wazuh GPG key**

   ```bash
   curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH \
     | sudo gpg --dearmour -o /usr/share/keyrings/wazuh-archive-keyring.gpg
   ```

2. **Download & run installer**

   ```bash
   curl -sO https://packages.wazuh.com/4.12/wazuh-install.sh && sudo bash ./wazuh-install.sh -a -i
   ```

   - `-a` installs all components (manager, indexer, Kibana plugin)
   - `-i` enables interactive mode

## 3. Access your Wazuh Dashboard:

```bash
	https://your-ubuntu-vm-ip
```

## 4. Configure Windows Agent

1. **Download & install** Follow the official guide: [https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-windows.html](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-windows.html)

2. **Point agent to manager**  `Enter your Windows Host's IP` 
![[wazuh-agent.png]]

3. **Register agent on Manager(Ubuntu Server)**

   ```bash
   sudo /var/ossec/bin/manage_agents
   # Follow prompts to add and extract the agent authentication key
   ```
   
![[setting-up.png]]

4. **Save and Restart agent service on windows host**
. 
![[restart 1.jpg]]
## 5. Test File-Integrity Monitoring

1. **On Windows host**, open agent config: `C:\Program Files (x86)\ossec-agent\ossec.conf`

2. **Add monitored directory** Under ` <!-- File integrity monitoring -->`

   ```xml
   <directories realtime="yes">C:\Users\CH1N7U\Documents\wazuh-test</directories>
   ```

3. **Create and Delete a test file**

   ```powershell
    Test-1.txt
   ```

4. **Verify alert**

   - In Wazuh, navigate to **File Integrity Monitoring --> Events**
  ![[logs.png]]

---

## 6. Validation & Next Steps

- **Log in to Wazuh:** `https://192.168.1.50:5601` (default credentials: `admin` / `<provided during install>`)
- **Explore Dashboards:** Out-of-the-box Wazuh dashboards for agent status, security events, FIM alerts.
- **Extend monitoring:**
  - Linux agent on other VMs
  - Custom rules for Windows event channels
  - Integrate Suricata or Zeek for network telemetry

---

## 7. Conclusion

We now have a fully functional SIEM lab:

- **Wazuh Manager** collecting and indexing logs
- **Wazuh dashboards** for visualization
- **Windows agent** sending file-integrity and security events

## My System Specifications:
   ```powershell
    CPU: i5-13420H
	RAM: 32gb DDR5
	GPU: RTX 3050 6gb
	Storage: 1tb NVME Gen 4
	Displays: 3
   ```
