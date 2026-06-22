<h1>
  <img src="../../assets/VKS.png" style="height:30px"; vertical-align:middle;> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying the VKS Supervisor with "NSX + DTGW/VNA"** within a vSphere environment.

* [**Requirements**](#requirements)
* [Supervisor Deployment](2b-deployment.md)
* [Deployment App (VMs)](2c-deployment-vms.md)
* [Deployment App (k8s)](2d-deployment-k8s.md)
</div>

<div markdown>
![VDS Architecture](images/0-DTGW.jpg){ width="100%" }
</div>
</div>

---

## Requirements {: #requirements }

![Topology](images/2a-1-Topology.jpg){: .center style="width:80%" }

VKS Supervisor with "VDS + FLB" has the following Networking requirements:  

* **Physical Fabric:**  
    * **2 subnets/VLAN**  
        * **Management**:  
          Can be an existing Management subnet/VLAN hosting already other components such as vCenter. 6 consicutive IP@ in that subnet are needed (xxx 4 for Supervisor Mgt + 2 for FLB Mgt ???).
        * **Dataplane**:  
          Can be an existing subnet/VLAN hosting already other components such as Physical Servers. A large amount is needed though (for K8s Controller and Worker Nodes IPs and optionally VIPs).



