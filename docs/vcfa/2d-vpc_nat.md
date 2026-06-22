<h1>
  <img src="../../assets/VCFA.png" style="height:30px"; vertical-align:middle;> VPC NAT Configuration in VCF-A Tenant
</h1>

<div class="grid" markdown style="grid-template-columns: 80% 20%">

<div markdown>

This section describes the procedures for configuring Network Address Translation (NAT) for your VPC workloads in private subnets.
<br><br>
**NAT** allows VPC Private subnets to communicate with external networks or provides a way for external users to reach private VPC workloads.
</div>

<div markdown>
![vCenter VPC Connectivity](images/1c-0-VPC_NAT.jpg){ width="100%" }
</div>

</div>


Attention if "#full-nat" used in 1d-ip_block_tenant.md

---

## Overview of NAT types

Different NAT types are available:

| Type | Use Case | Routing Logic |
| :--- | :--- | :--- |
| [**External-IP<br>(1:1)**](#ext-ip) | Maps a single Public IP to a single Private IP for specific workloads. | Provides Bi-directional communication (Inbound/Outbound) for Private VPC and TGW workloads. |
| [**Outbound-SNAT<br>(N:1 SNAT)**](#outbound-snat)| Allows multiple workloads to share a single Public IP for external access. | Supports outbound requests only; hides internal IP addresses from the physical network. |
| [**NAT<br>(SNAT/DNAT)**](#full-nat) | Provides stateful Layer 4 translation for granular traffic control. | Enables flexible port-level translation and mapping between Private subnets and external networks. |

![VPC NAT](images/1c-0-VPC_NAT_types.jpg){: .center style="width:90%" }

---

## External-IP (1:1 NAT) {: #ext-ip }

Maps a single Public IP to a single Private IP for specific workloads.

![VPC NAT Ext-IP](images/1c-1-VPC_NAT_ExtIP.jpg){: .center style="width:70%" }

### Configuration

#### Step1. Create a new External IP
![vCenter Create External IP](images/1c-1a-Create_VPC_NAT_ExtIP.jpg){ width="90%" style="display: block; margin: 0 auto;" }

### Monitoring
#### Show External IPs
![vCenter Result VPC Subnet](images/1c-1b-Validation_VPC_NAT_ExtIP.jpg){ width="90%" style="display: block; margin: 0 auto;" }

---
## Configuration Outbound-SNAT (N:1 SNAT) {: #outbound-snat }

Allows multiple workloads to share a single Public IP for external access.

![VPC NAT Ext-IP](images/1c-2-VPC_ONAT.jpg){: .center style="width:70%" }

### Configuration
<div style="margin-left: 40px; margin-right: 40px;" markdown="1">
!!! warning "Limitation"
    Outbound-SNAT is **not supported** on VPCs connected to Distributed Transit Gateways without VNA.  
    (only supported on VPCs connected to Centralized Transit Gateways or Distributed Transit Gateways with VNA)
</div>

#### Step1. Check Outbound-SNAT configuration in the VPC Gateway
![vCenter Check Outbound SNAT Config](images/1c-2a-Check_VPC_ONAT_Config.jpg){ width="70%" style="display: block; margin: 0 auto;" }

#### Step2. If disabled, Edit the VPC to find the Connectivity Profile used by the VPC
![vCenter Validation VPC Subnet](images/1c-2b-Find_Connectivity_Profile.jpg){ width="70%" style="display: block; margin: 0 auto;" }

#### Step3. Edit the Connectivity Profile and enable Outbound-SNAT
![vCenter Validation VPC Subnet](images/1c-2c-Enable_ONAT.jpg){ width="90%" style="display: block; margin: 0 auto;" }

### Monitoring
#### Show the Outbound-SNAT IP used by the VPC
![vCenter Result VPC Subnet](images/1c-2d-Validation_VPC_ONAT.jpg){ width="80%" style="display: block; margin: 0 auto;" }

---

## NAT (SNAT/DNAT) {: #full-nat }

Provides stateful Layer 4 translation for granular traffic control.

xxx To correct picture.

![VPC NAT Ext-IP](images/1c-3-VPC_NAT.jpg){: .center style="width:70%" }

### Configuration

To do...



---

