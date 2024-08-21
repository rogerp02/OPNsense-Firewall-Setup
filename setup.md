# OPNsense Setup Guide

This guide provides step-by-step instructions to set up OPNsense as your firewall/router, including configuring network interfaces, VLANs, firewall rules.

---

## **1. Prerequisites**

Before you start, ensure you have:
- A device with multiple network interfaces to run OPNsense (e.g., a physical server, dedicated firewall appliance, or a virtual machine).
- Basic networking knowledge and familiarity with your hypervisor or hardware platform.

---

## **2. Network Interface Setup**

### **2.1 Understanding Your Network Layout**

Decide how you want to structure your network:
- **WAN**: The interface connected to your ISP or upstream router.
- **LAN**: The main internal network interface.
- **Optional Networks**: You may also want separate interfaces for:
  - **IoT devices**
  - **Management network**
  - **DMZ (for isolated servers)**

### **2.2 Identifying Your Network Interfaces**

1. During the initial OPNsense setup, you’ll be prompted to assign interfaces:
   - Typically, the first detected interface is assigned as the **WAN**.
   - The next detected interface becomes the **LAN**.
   - Additional interfaces can be assigned to specific roles (e.g., IoT, Management).

2. If you’re unsure which interface corresponds to which port, test by connecting a device and checking which interface shows activity.

---

## **3. Initial OPNsense Configuration**

### **3.1 Accessing the OPNsense Web Interface**

1. Connect a device to the LAN interface.
2. Open a web browser and navigate to `192.168.1.1` (default LAN IP) or to the IP address you assigned to the LAN interface.
3. Log in with the default credentials:
   - Username: `root`
   - Password: `opnsense`

### **3.2 Configuring Interfaces**

1. Go to **Interfaces > [Interface Name]** and configure each interface:
   - **WAN**: Typically set to DHCP or a static IP based on your ISP’s requirements.
   - **LAN**: Assign a static IP (e.g., `192.168.1.1/24`).
   - **Optional Networks** (e.g., IoT, Management): Configure each with appropriate IP ranges.

2. For each interface:
   - Enable **Block Private Networks** on the **WAN** if connected directly to the internet.
   - Enable **Block Bogon Networks** on the **WAN**.

### **3.3 VLAN Setup (Optional)**

1. If using VLANs, go to **Interfaces > Other Types > VLAN**.
2. Create VLANs under the desired physical interface (e.g., VLAN 10 for LAN, VLAN 20 for IoT).
3. Assign the VLANs to interfaces and configure them like any other interface.

---

## **4. Firewall Rules and Security**

### **4.1 Configuring Firewall Rules**

1. Go to **Firewall > Rules** and select the interface you want to configure.
2. Set up rules to allow, block, or restrict traffic as needed:
   - Example: Allow all outgoing traffic from LAN but block incoming traffic.
   - Isolate the IoT network from the main LAN by blocking inter-VLAN traffic.

### **4.2 NAT and Port Forwarding**

1. Configure port forwarding under **Firewall > NAT > Port Forward** for services that need external access (e.g., a web server or game server).
2. Create rules that specify the source port, destination, and allowed services.

---

## **5. NAT Configuration**

Network Address Translation (NAT) is essential for translating private IP addresses within your network to a public IP address, allowing devices on your LAN to access the internet.

### **5.1 Configuring Outbound NAT**

1. Navigate to **Firewall > NAT > Outbound**.
2. Set the mode to **Hybrid** or **Manual** depending on your needs:
   - **Automatic**: OPNsense automatically handles NAT for outbound traffic.
   - **Hybrid**: Allows manual rules while still handling automatic rules.
   - **Manual**: Complete control over outbound NAT rules.
3. If using **Hybrid** or **Manual** mode, add specific rules for outbound traffic from each interface that requires NAT (e.g., LAN, IoT).

### **5.2 Configuring Port Forwarding (Inbound NAT)**

1. Go to **Firewall > NAT > Port Forward** to set up port forwarding for inbound traffic.
2. Specify the interface (e.g., WAN), destination IP, and port, as well as the internal IP and port where traffic should be forwarded.

### **5.3 Testing and Troubleshooting NAT**

1. After setting up NAT, ensure that devices can access the internet and that any port forwarding rules are functioning as intended.
2. Use **Diagnostics > Ping** or **Traceroute** to verify connectivity.

---

## **6. VLAN and Multi-Network Setup**

### **6.1 Segmenting Traffic with VLANs**

1. Use VLANs to separate traffic if you only have a limited number of physical interfaces.
2. Assign VLANs to each network segment (e.g., VLAN 10 for LAN, VLAN 20 for IoT).

### **6.2 Configuring DHCP for Each Network**

1. Go to **Services > DHCPv4 > [Interface]**.
2. Enable DHCP for each network segment, specifying the range and DNS settings.

---

## **7. Security Best Practices**

1. **Block RFC1918 Private Networks and Bogon Networks**:
   - For WAN interfaces, block these networks to prevent unauthorized access and spoofing attacks.

2. **Isolate IoT Devices**:
   - Ensure your IoT network is isolated from sensitive networks like your LAN.

3. **Regular Updates**:
   - Keep OPNsense updated under **System > Firmware > Updates**.

---

## **8. Troubleshooting and Monitoring**

1. **Check Interface Status**:
   - Go to **Interfaces > Overview** to see link status and activity.
   
2. **Log Monitoring**:
   - Use **System > Log Files > Firewall** to view blocked or allowed traffic.

---

## **9. Backup and Restore**

1. Once everything is configured, back up your settings under **System > Configuration > Backups**.
2. Save your configuration regularly to avoid data loss.

---
