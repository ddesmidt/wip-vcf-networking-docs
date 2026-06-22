<h1>
  <img src="../../assets/vCenter.png" style="height:30px"; vertical-align:middle;> Connectivity Policy in vCenter
</h1>

<div class="grid" markdown style="grid-template-columns: 70% 30%">

<div markdown>
This section describes the procedures for configuring Connectivity Policy using the vSphere Client.
<br><br>
**Connectivity Policy** defines cross-VPC communication rules.
</div>


<div markdown>
![vCenter Conn Policy](images/3f-0-Conn_Policy.jpg){ width="100%" }
</div>

</div>

---

## Overview of Connectivity Policy Types

Different Connectivity Policy types are available:

| Type | Use Case | Routing Logic |
| :--- | :--- | :--- |
| [**Community**](#community) | Permits communication between VPCs within the same defined group. Ideal for application-tier connectivity within a specific division. | VPCs can communicate with other **Community** peers in their group and all **Promiscuous** VPCs. |
| [**Isolated**](#isolated)| Strictly blocks communication to all other VPCs. Best for highly sensitive or standalone application workloads. | VPCs are restricted to communicating only with **Promiscuous** VPCs (Shared Services). |
| [**Promiscuous**](#promiscuous)| Provides universal connectivity across the environment. Ideal for shared  services (e.g., Backup, DNS, NTP). | VPCs have unrestricted communication with **all** other VPCs in the environment. |

![vCenter Conn Policy Types](images/3f-0-Conn_Policy_Types.jpg){: .center style="width:80%" }

---

## Connectivity Policy

### Configuration

<div style="margin-left: 20px; margin-right: 20px;" markdown="1">
??? info ":material-information-outline: Deep Dive: Blogs & Video Demonstrations"
    * **Video Walkthrough:** Watch the step-by-step Connectivity Policy configuration [<span style="color: #FF0000;">:fontawesome-brands-youtube:</span> on YouTube](https://youtu.be/_pdVqTBho98){ target="_blank" }.
    * **Technical Blog:** Read the detailed Connectivity Policy blog on the [<span style="color: #007cbb;">:material-newspaper-variant-outline:</span> VMware Cloud Foundation Blog](https://blogs.vmware.com/cloud-foundation/2026/05/15/vcf-networking-9-1-simpler-vpc-connectivity-control/){ target="_blank" }.
</div>

#### Step1. Create Connectivity Policy
![vCenter Conn Policy config](images/3f-1a-Create_ConnPolicy.jpg){ width="95%" style="display: block; margin: 0 auto;" }

* **Visibility**:  
  Set to External.

* **CIDRs/Ranges**:  
  Enter the specific CIDR blocks or IP ranges to be managed by this block.  

  
* **Excluded IP Ranges**:  
  (Optional) Specify any IP Range(s) within the CIDRs above that should be withheld from automatic allocation (e.g. IP Range used by the physical network).
  
* **Reserved for Specific Subnet**:  
  Enable for the Subnet-VLAN use case, otherwise disabled.

### Monitoring

#### Show other VPCs within your community

![vCenter Conn Policy validation](images/3f-1b-Validation_ConnPolicy_Members.jpg){ width="80%" style="display: block; margin: 0 auto;" }

#### Show VPCs who don't belog to any Community

![vCenter Conn Policy validation](images/3f-1b-Validation_VPC_NoConnPolicy.jpg){ width="80%" style="display: block; margin: 0 auto;" }


#### Show all VPCs with connectivity to yours

xxx to do
---
