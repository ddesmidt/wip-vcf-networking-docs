<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor Network Services
</h1>

This section describes the procedures for **deploying the Supervisor** within a vSphere environment.

---

## Different network options for Supervisor deployment

### 1. Without NSX

**Architecture:** Uses VDS port groups and deploys or consume load balancers to handle load balancing traffic.  
**Best for:** VMware vSphere Foundation (VVF), lab environments, Proof of Concepts (PoC), or smaller environments.  
**Limitation:** Limited Network Services (only LB) and limited VCF Automation support (no namespace create/delete/update from VCFA).

* **1a. VDS + FLB**  
![VDS Architecture](images/0-VDS.jpg){ width="90%" style="display: block; margin: 0 auto; max-width: 250px;" } 

| Metric / Feature | **1a. VDS + FLB** |
| :--- | :--- |
| **Physical Fabric** | MTU ≥ 1500,<br> L2 Physical |
| **VCF-A Support** | 🔴 Limited <br>(No Multi-Tenancy, no namespace CRUD) |
| **Network Services** | 🔴 Limited <br>(No Subnets, No NAT, No VPN) |
| **Scale** | 🔴 Limited <br>(All VMs and K8s consume a Public IP) |
| **Security** | 🔴 Direct Access <br>(K8s Nodes reachable from external) |

---

### 2. With NSX

**Architecture:** Uses NSX Distributed or Centralized Transit Gateways and VNA or Edge Cluster to handle load balancing traffic.  
**Best for:** Fully integrated VCF architecture for better scale and security.  
**Consideration:** Requires fully deployed NSX overlay infrastructure with DTGW+VNA or CTGW+Edge+T0.

* **2a. NSX + CTGW (TEP)**
* **2b. NSX + DTGW VLAN (TEP)**
* **2c. NSX + DTGW VXLAN (TEP)** *(From VCF 9.1.1+)*
* **2d. NSX + DTGW VLAN (TEP-Less)** *(From VCF 9.1.1+)*

| <div style="width: 100px;">Metric /Feature</div> | <div style="min-width: 140px;">**2a. NSX +<br>CTGW(TEP)**</div> | <div style="min-width: 140px;">**2b. NSX +<br>DTGW VLAN(TEP)**</div> | <div style="min-width: 150px;">**2c. NSX +<br>DTGW VXLAN(TEP)**</div> | <div style="min-width: 140px;">**2d. NSX +<br>DTGW VLAN(TEP-Less)**</div> |
| :--- | :--- | :--- | :--- | :--- |
| **Physical Fabric** | MTU ≥ 1700,<br> BGP | MTU ≥ 1700,<br> L2 Physical | MTU ≥ 1700,<br> EVPN + BGP | MTU ≥ 1500,<br> L2 Physical |
| **VCF-A Support** | 🟢 Yes | 🟢 Yes | 🟢 Yes | 🟢 Yes |
| **Network Services** | 🟢 All | 🟢 Most <br>(All but VPN) | 🟢 Most <br>(All but VPN) | 🔴 Limited <br>(No Subnets private, No NAT, No VPN, no Multicast/VRRP/HSRP/etc) |
| **Scale** | 🟢 Large | 🟢 Large | 🟢 Large | 🔴 Limited <br>(Public IPs for VMs and K8s) |
| **Security** | 🟢 Isolated | 🟢 Isolated | 🟢 Isolated | 🔴 Direct Access <br>(K8s Nodes) |

---


??? info "Detailed Architecture Pros & Cons"
    **1. Supervisor with ["VDS + FLB"](1a-requirements.md)**
    
    * **Pros:**
        * **Footprint:** Slightly smaller overall footprint (the FLB appliance is slightly smaller than a VNA)
        * **VMware Editions:** Available in VMware vSphere Foundation (VVF) and VMware Cloud Foundation (VCF)
    * **Cons:**
        * **Network Services Limitations:** Lacks full support of Network Services such as Subnets, Static Routes, NAT
        * **VCF Integration Limitations:** Lacks full support for other VCF components, specifically VCF Automation (VCF-A)
        * **Scale:** All VIPs are managed by a single FLB's Active/Standby (A/S)
    * **Requirements:**
        * **L2 Connectivity:** L2/VLAN connectivity required across vCenter cluster(s)

    ---

    **2. Supervisor with ["NSX + DTGW"](2a-requirements.md) or ["NSX + CTGW"](3a-requirements.md)**

    * **Pros:**
        * **Security:**  
            * No possible external access directly to K8s Nodes
            * Option to control application communication including:
                * Cross-Container via Antrea CNI (DFW)
                * Cross-VPC VIP communication (VPC Connectivity Policies)
        * **Scale:**
            * Uses fewer public IPs (K8s nodes use private IPs)
            * VIPs are highly scalable, distributed across up to 10 VNA Nodes in an Active/Active (A/A) configuration
    * **Cons:**
        * **VMware Editions:** Only available in VMware Cloud Foundation (VCF) (not VMware vSphere Foundation (VVF))
        * **Operations:** Requires NSX (though installation and management remain very simple)
        * **Footprint:** Slightly larger footprint (the VNA is slightly larger than the FLB)
    * **Requirements:**
        * **For both DTGW/CTGW:** Large MTU (min 1700 - recommended 9000)
        * **For DTGW:** L2/VLAN connectivity required across vCenter cluster(s)
        * **For CTGW:** Physical network with BGP routing protocol

??? note "Upcoming Architectures"
    The following architectures will be covered in future updates:  

    * Avi Load Balancer  

---

!!! info "Document Versioning"
    This guide is updated for **VCF 9.1+**.  
    If you are running an older version, some options may not be available.

---