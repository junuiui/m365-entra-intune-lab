# Microsoft 365, Azure Entra ID & Intune Administration Lab

Enterprise cloud identity management, security compliance, and endpoint deployment using Microsoft 365 Developer E5 tenant and Microsoft Intune (MDM).

## Table of Contents
1. [Overview](#1-overview)
2. [Azure Entra ID Identity Management](#2-azure-entra-id-identity-management)
3. [Microsoft 365 Tenant Administration](#3-microsoft-365-tenant-administration)
4. [Microsoft Intune (MDM) & Endpoint Security](#4-microsoft-intune-mdm--endpoint-security)
5. [Security & Compliance Policies](#5-security--compliance-policies)

---

## 1. Overview
This project demonstrates cloud-based identity and endpoint management in a simulated corporate environment. It covers user lifecycle management, cloud security baselines, conditional access policies, and automated device compliance via Microsoft Intune.

---

## 2. Azure Entra ID Identity Management
* **User & Group Provisioning:** Created and managed cloud user accounts, department-based security groups, and distribution lists.
* **Role-Based Access Control (RBAC):** Assigned granular administrative roles (e.g., Helpdesk Administrator) to simulate principle of least privilege.

---

## 3. Microsoft 365 Tenant Administration
* **License Management:** Provisioned and managed Microsoft 365 Business Premium / E5 licenses for users.
* **Exchange Online:** Configured shared mailboxes, mail flow rules, and anti-spam/anti-malware policies.

---

## 4. Microsoft Intune (MDM) & Endpoint Security
* **Device Enrollment:** Joined Windows 11 Enterprise client VMs to Azure Entra ID and enrolled them into Microsoft Intune.
* **Silent App Deployment:** Deployed automated software installation packages (e.g., core utility tools) remotely across managed endpoints.

---

## 5. Security & Compliance Policies
* **Conditional Access Policies:** Enforced Multi-Factor Authentication (MFA) and blocked access from non-compliant locations/devices.
* **Device Compliance:** Configured security baselines requiring BitLocker drive encryption and minimum password complexity before granting corporate resource access.
