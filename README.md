# Fortigate-FortiWifi-60E
# 🛡️ FortiWifi 60E Home Lab Security & Remote Access Implementation

## Project Overview
This repository documents the complete configuration and policy deployment for a FortiWifi 60E firewall used in a home lab environment. The goal was to establish secure remote access (SSL VPN), manage inbound connections (RDP), and securely facilitate external file sharing, all while implementing robust firewall policies and network segmentation.

### Key Technologies Demonstrated
* **Hardware:** FortiWifi 60E Firewall/Wireless Access Point
* **Networking:** Static Routing, VLANs (implied for LAN IP)
* **Security:** SSL VPN (Remote Access), Local User Authentication
* **Policy:** Firewall Objects, Virtual IPs (VIPs) for RDP/File Share
* **Services:** RDP, SMB/File Sharing

* Initial Configuration
* ## ⚙️ Initial Device Setup

This phase involved bringing the FortiWifi online and securing management access.

| Configuration Step | Description | Commands/Notes (Conceptual) |
| :--- | :--- | :--- |
| **Internal LAN IP Assignment** | Configured the internal interface (e.g., `port1` or `internal`) with a static IP and DHCP server to establish the internal network (e.g., `192.168.1.1/24`). | Demonstrated proficiency in interface and network addressing configuration. |
| **Admin Account Setup** | Created a dedicated, non-default administrative user with strong authentication for management access. | Essential security hardening by replacing/securing default accounts. |
| **Firmware Update** | Ensured the FortiWifi was running the latest recommended FortiOS version for stability and security patches. | Adherence to security best practices. |

Remote Access Configuration (SSL VPN)

## 🔒 Secure Remote Access (SSL VPN)

SSL VPN was implemented to provide external users with secure, encrypted access to the internal network resources.

### 3.1 SSL VPN User Accounts
* **Method:** Local User Database was used to create individual user accounts with strong, unique passwords.
    * *Accounts Created:* (e.g., `user_remote`, `user_admin_vpn`)
* **Purpose:** Enforces the principle of least privilege, allowing specific access controls per user.

### 3.2 SSL VPN Configuration
| Configuration Step | Detail |
| :--- | :--- |
| **Tunnel Setup** | Configured the SSL VPN portal and tunnel mode (Full Tunnel vs. Split Tunnel). |
| **Address Range** | Defined the specific IP address pool (e.g., `10.212.134.x`) that VPN clients receive upon connection. |
| **Authentication** | Tied the VPN policy to the local user group created in Step 3.1. |

4. Application and Service Exposure
   ## 🌐 Service Access Configuration

### 4.1 Inbound RDP Connections (Public IP to Internal Host)
RDP access was secured using **Virtual IPs (VIPs)** and specific policies to allow external connections to internal RDP servers.

* **VIP Creation:** Mapped a specific external port on the FortiWifi's WAN interface to the internal IP and RDP port (`3389`) of the target server.
* **Security:** Used non-standard external ports for obfuscation (e.g., mapping external port `43389` to internal port `3389`).

### 4.2 External File Sharing (SMB/Samba)
A secure method was established to allow specific external users to access an internal File Share server (SMB/Samba).

* **Policy Type:** Similar to RDP, a dedicated **VIP** was used to NAT the external connection to the internal file server's IP and port (`445`).
* **Access Control:** Access was restricted to the VPN Address Range, ensuring only connected VPN users could reach the file share VIP.
* 
  5. Policy Deployment (The Security Layer)
  ## 🧱 Firewall Policy and Object Creation

All access to and through the network requires explicit firewall policies and objects. This ensures traffic is only allowed if it meets specified criteria.

### Key Firewall Objects Created:
| Object Type | Example Object Name | Purpose |
| :--- | :--- | :--- |
| **Addresses** | `LAN_Internal_Servers`, `FileShare_Host` | Defines specific internal IP addresses/ranges for use in policies. |
| **Services** | `SVC_TCP_RDP_3389`, `SVC_TCP_SMB_445` | Defines the specific ports required for each application. |
| **VIPs** | `VIP_External_RDP`, `VIP_External_FileShare` | Maps the public/WAN IP and port to the internal servers. |

### Implemented Policy Flow:
* **SSL-VPN-to-LAN Policy:** Allows authenticated VPN users (source: VPN address pool) to access internal resources (dest: `LAN_Internal_Servers`) via required services (e.g., PING, RDP, SMB).
* **WAN-to-LAN Policy (RDP):** Allows internet traffic (source: `all`) to hit the `VIP_External_RDP` object, permitting traffic only to the RDP server.
* **WAN-to-LAN Policy (File Share):** (If necessary) A highly restricted policy allowing only trusted external source IPs to access the `VIP_External_FileShare`.

  
