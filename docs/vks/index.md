<h1>
  <img src="../assets/VKS.png" style="height:30px; vertical-align:middle;"> VKS Network Services
</h1>

This section describes the procedures for **deploying the VKS Supervisor** within a vSphere environment.

---

## Different network options for VKS Supervisor deployment  
Explore the specific network architectures available for your VKS Supervisor cluster:

| <div style="width: 20%;">Architecture Option</div> | <div style="min-width: 130px; white-space: nowrap;">Network Stack</div> | <div style="width: 65%;">Description & Use Case</div> |
| :--- | :--- | :--- |
| **1. VDS + FLB** | VLAN-Backed | ![VDS Architecture](images/0-VDS.jpg){ width="90%" style="display: block; margin: 0 auto; max-width: 300px;" } <br> **Uses VDS port groups** for the Supervisor Cluster and deploys **FLB virtual appliances** to handle load balancing traffic. <br><br> **Best for:** Lab environments, Proof of Concepts (PoC), or smaller environments. <br><br> **Limitation:** Not supported with VCF Automation. |
| **2. NSX + DTGW/VNA** | NSX Overlay | ![DTGW Architecture](images/0-DTGW.jpg){ width="90%" style="display: block; margin: 0 auto; max-width: 300px;" } <br> **Uses NSX Distributed Transit Gateways + VNA** for the Supervisor Cluster. <br><br> **Best for:** Fully integrated VCF architecture for better scale and security. <br><br> **Consideration:** Requires NSX. |

!!! note "Upcoming Architectures"
    The following architectures will be covered in future updates:  
    
    * NSX + CTGW/Edge  
    * Avi Load Balancer  

---

!!! info "Document Versioning"
    This guide is updated for **VCF 9.1+**.  
    If you are running an older version, some options may not be available.

---