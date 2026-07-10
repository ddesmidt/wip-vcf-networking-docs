<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "VDS + FLB"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the requirements for **deploying the Supervisor utilizing a "VDS + FLB" architecture** inside a vSphere environment.

* [**Requirements**](#requirements)
</div>

<div markdown>
![VDS Architecture](images/0-VDS.jpg){ width="100%" }
</div>
</div>

---

## Requirements {: #requirements }

VKS Supervisor with "VDS + FLB" has the following networking requirements:  
![Topology](images/1a-1-Topology.jpg){ width="80%" style="display: block; margin: 0 auto;" }

### Physical Fabric {: #physical_fabric }

#### **2 or 3 Subnets/VLANs**  
* **Management**:  
    Can be an existing Management subnet/VLAN that already hosts other components (such as vCenter).  
    Requires 7 consecutive IPs (5 for the Supervisor Cluster + 2 for the FLB VMs).
* **Dataplane**:  
    Can be an existing subnet/VLAN that already hosts other components (such as Physical Servers).  
    A large pool of IPs is required (for future K8s Controller nodes, Worker nodes, and VIPs).
* **(Optional) VIPs**:  
    Can be an existing subnet/VLAN that already hosts other components.  
    A large pool of IPs is required for future K8s VIPs.

***Note:** No requirement for dynamic routing (such as BGP).*

### vCenter {: #vcenter }

#### **VDS Port Groups**  
* **Management**:  
    VDS Port Group VLAN for the Management traffic.  

    ??? info "Status Validation"
        Navigate to **vCenter** > **Networking** > **VDS-PortGroup** > **Edit Settings** > **VLAN**.  
        Ensure the VLAN Type is "VLAN" with the right "VLAN ID":
        ![VDS Port Group Settings](images/1a-2a-VDS-PG.jpg){ width="95%" style="display: block; margin: 0 auto;" }

* **Dataplane**:  
    VDS Port Group VLAN for the K8s Dataplane traffic.

    ??? info "Status Validation"
        Navigate to **vCenter** > **Networking** > **VDS-PortGroup** > **Edit Settings** > **VLAN**.  
        Ensure the VLAN Type is "VLAN" with the right "VLAN ID":
        ![VDS Port Group Settings](images/1a-2b-VDS-PG.jpg){ width="95%" style="display: block; margin: 0 auto;" }

* **(Optional) VIPs**:  
    VDS Port Group VLAN for the K8s VIPs traffic.

    ??? info "Status Validation"
        Navigate to **vCenter** > **Networking** > **VDS-PortGroup** > **Edit Settings** > **VLAN**.  
        Ensure the VLAN Type is "VLAN" with the right "VLAN ID":
        ![VDS Port Group Settings](images/1a-2c-VDS-PG.jpg){ width="95%" style="display: block; margin: 0 auto;" }


