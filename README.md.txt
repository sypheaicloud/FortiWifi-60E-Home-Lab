# 🛡️ FortiWifi 60E Security, Remote Access, and Threat Prevention Implementation

## Project Overview
This repository documents the complete configuration and policy deployment for a FortiWifi 60E firewall used in a home lab environment. The project establishes secure remote access (SSL VPN) and implements **critical UTM controls, specifically Intrusion Prevention (IPS)**, on all entry points to the internal network.

### Key Technologies Demonstrated
* **Hardware:** FortiWifi 60E Firewall/Wireless Access Point
* **Security:** SSL VPN (Remote Access), Local User Authentication, **Intrusion Prevention System (IPS)**
* **Networking:** Static Addressing, Policy-based Network Address Translation (NAT)
* **Policy:** Firewall Objects, Virtual IPs (VIPs), Security Profile Application

---

## ⚙️ Initial Device Setup

This phase involved bringing the FortiWifi online and securing management access.

| Configuration Step | Description | Security / Networking Skill |
| :--- | :--- | :--- |
| **Internal LAN IP Assignment** | Configured the internal interface (e.g., `port1`) with a static IP and DHCP server to establish the internal network (e.g., `192.168.1.1/24`). | Demonstrated foundational networking setup. |
| **Admin Account Setup** | Created a dedicated, non-default administrative user with strong authentication and least privilege access. | Implemented strong access control and security hardening. |
| **Firmware Update** | Ensured the FortiWifi was running the latest recommended FortiOS version. | Adherence to vendor-recommended security maintenance. |

---

## 🔒 Secure Remote Access (SSL VPN)

SSL VPN was implemented to provide external users with secure, encrypted access to the internal network resources.

### 3.1 SSL VPN User Management
* **Method:** Created individual user accounts within the local user database.
* **Access Control:** User accounts were grouped and then tied to the VPN policy to control resource access based on role.

### 3.2 SSL VPN Tunnel Configuration
| Configuration Detail | Purpose |
| :--- | :--- |
| **Tunnel Setup** | Configured the SSL VPN web portal and established tunnel mode operation for full client-to-LAN connectivity. |
| **Address Range** | Defined the specific IP address pool (e.g., `10.212.134.x`) that VPN clients receive upon connection. |
| **Authentication Policy** | Created the policy allowing traffic from the VPN interface (`ssl.root`) to the internal LAN interface, permitting full access only after successful user authentication. |

---

## 🌐 Service Exposure and Inbound Configuration

### 4.1 Inbound RDP Connections
RDP access was secured using **Virtual IPs (VIPs)** and specific policies to allow external connections to internal RDP servers.

* **VIP Configuration:** Mapped a non-standard external port on the FortiWifi's WAN interface to the internal server's private IP and RDP port (`3389`).
* **Security Layer:** This access is strictly controlled by a dedicated firewall policy where the **Intrusion Prevention Profile** is enforced.

### 4.2 External File Sharing (SMB/Samba)
A secure method was established to allow specific external users to access an internal File Share server (SMB/Samba).

* **Policy Type:** A dedicated **VIP** was used to NAT the external connection to the internal file server's IP and port (`445`).
* **Security Layer:** Access was restricted to the VPN Address Range, and all traffic is subject to the IPS policy.

---

## 🛡️ Advanced Threat Prevention (Intrusion Prevention System)

A customized IPS profile was created and applied to all policies that accept traffic from external or less-trusted sources (WAN-to-LAN and SSL-VPN-to-LAN).

### IPS Implementation Details:
* **Profile Creation:** A custom profile (`Inbound_Aggressive_IPS`) was created to ensure maximum protection with minimal false positives.
* **Signature Filtering:** Enabled all **Critical** and **High** severity signatures, with the default action set to **Block** or **Reset**.
* **Protocol Enforcement:** Enabled deep packet inspection for inbound protocols including **RDP** and **SMB** to detect application-layer exploits specific to those services.
* **Policy Application:** The `Inbound_Aggressive_IPS` profile was applied to all policies allowing traffic into the internal LAN (VPN access, RDP VIP, File Share VIP).

---

## 🧱 Firewall Policy and Object Summary

All access requires explicit firewall policies and objects, reinforcing the principle of **Default Deny**.

### Key Policy Objects Created:
* **Addresses:** Defined specific internal IP addresses/ranges (`LAN_Internal_Servers`, `FileShare_Host`).
* **Services:** Defined the specific ports required for each application (`SVC_TCP_RDP_3389`, `SVC_TCP_SMB_445`).
* **VIPs:** Created mappings for external access (`VIP_External_RDP`, `VIP_External_FileShare`).

---
### 💡 Project Summary & Skills Demonstrated

* **Network Segmentation & Hardening:** Successfully isolated internal services while providing controlled external access.
* **Threat Mitigation:** Proactively implemented IPS to protect exposed services from known exploits and vulnerabilities.
* **Access Control:** Enforced strong authentication and authorization through local user accounts and policy design.