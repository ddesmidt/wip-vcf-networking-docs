<h1>
  <img src="../../assets/vCenter.png" style="height:30px"; vertical-align:middle;> Edge Configuration in vCenter
</h1>

<div class="grid" markdown style="grid-template-columns: 80% 20%">

<div markdown>

This section describes the procedures for configuring NSX Edge Clusters and Nodes using the vSphere Client.
<br><br>
**NSX Edge Nodes** provide the centralized network services required **for Centralized Transit Gateway (CTGW)** designs within the VPC architecture.
</div>


<div markdown>
![vCenter Edge](images/2a-0-Edge.jpg){ width="100%" }
</div>

</div>

---

## Edge Cluster / Edge Nodes

### Configuration

<div style="margin-left: 20px; margin-right: 20px;" markdown="1">
??? info ":material-information-outline: Deep Dive: Blogs & Video Demonstrations"
    * **Video Walkthrough:** Watch the step-by-step Edge Cluster & Edge Node deployment guide [<span style="color: #FF0000;">:fontawesome-brands-youtube:</span> on YouTube](https://www.youtube.com/watch?v=ruoprGnf_v8){ target="_blank" }.
    * **Technical Blog:** Read the detailed architectural breakdown on the [<span style="color: #007cbb;">:material-newspaper-variant-outline:</span> VMware Cloud Foundation Blog](https://blogs.vmware.com/cloud-foundation/2025/06/25/vpc-centralized-network-connectivity-with-guided-edge-deployment/){ target="_blank" }.
</div>

#### Step1. Create new Edge Cluster / Edge Nodes
![vCenter Edge Cluster](images/2a-1-Create_Edge.jpg){ width="80%" style="display: block; margin: 0 auto;" }

#### Step2. Configure Edge Cluster
![vCenter Edge Cluster config](images/2a-2-Create_Edge.jpg){ width="70%" style="display: block; margin: 0 auto;" }

* **Node Form Factor**:  
  Select the appliance size (vCPU / Memory) of the Edge Nodes.  
  This determines the scale and performance limits for the logical routers (Tier-0, CTGW, and VPC) hosted on the node.  
  More information on [VMware NSX Configmax](https://configmax.broadcom.com/guest?vmwareproduct=NSX).

* **Auto generate passwords and manage them via SDDC Manager**  
  Enabling this option automates password generation and allows SDDC Manager to handle credential lifecycle management.

#### Step3. Configure Edge Nodes Placement and Networking
![vCenter Edge Node](images/2a-3a-Configure_EdgeNode1.jpg){ width="90%" style="display: block; margin: 0 auto;" }

* **vSphere Cluster / Resource Pool / Host Group Affinity / Data Store**:  
  Defines the physical placement of the Edge Node VM.  
  You can optionally specify Resource Pools and Host Group Affinity to control where the VM resides within the cluster.  

* **Management Network Settings (IP Address Type / IP Assignment / Port Group)**  
  Configures the IP assignment and Port Group for the Edge Node management interface.

* **Uplinks (Edge Node Uplink Mapping / TEP VLAN)**  
  Defines vNIC connectivity and Overlay TEP configuration.  
  Note: To share the same VLAN for both ESXi TEPs and Edge Node TEPs, you must enable "Use the host overlay network configuration from the selected vSphere Cluster."  

    <div style="margin-left: 40px; margin-right: 40px;" markdown="1">
    ??? info "In case "Use the host overlay network configuration from the selected vSphere Cluster" is greyed out"
        If this option is greyed out, follow these steps:  
        1. Go to **NSX Manager**.  
        2. Navigate to **System** > **Fabric** > **Nodes** > **Settings**.  
        3. Enable the **NSX on DVPG** toggle.  

        !!! warning "Security Consideration: Microsegmentation / DFW"
            Enabling "NSX on DVPG" activates the **Distributed Firewall (DFW)** for all VMs connected to VDS Port Groups. 
            **Critical:** Ensure no "Deny All" rules are active in your DFW policy before enabling this, as it may immediately block traffic to existing workloads on those Port Groups.
            
        ![NSX on DVPGs](images/2a-3b-Enable_NSX_on_DVPGs.jpg){ width="90%" style="display: block; margin: 0 auto;" }
    </div>

* **VLAN MTU Check**  
  (Optional) Validates the MTU settings on the selected VLAN to ensure overlay traffic is not fragmented.

**Repeat these steps for the remaining Edge Nodes (2+ nodes recommended).**

<div style="margin-left: 40px; margin-right: 40px;" markdown="1">
??? info "Cloning option"
    In case other Edge Nodes have the vSphere Cluster and Uplink settings, use the cloning option.
    ![Add Edge Node2](images/2a-3c-Configure_EdgeNode2.jpg){ width="50%" style="display: block; margin: 0 auto;" }
</div>

### Monitoring
#### Status
The status reflects the successful application of the configuration.
![vCenter Edge Status](images/2a-4-Validation_Edge.jpg){ width="90%" style="display: block; margin: 0 auto;" }

---
