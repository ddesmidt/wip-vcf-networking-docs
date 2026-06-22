<h1>
  <img src="../../assets/vCenter.png" style="height:30px"; vertical-align:middle;> Connectivity Profile in vCenter
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>
This section describes the procedures for configuring  Connectivity Profile using the vSphere Client.
<br><br>
**Connectivity Profile** defines the VPC's connection to the Transit Gateway, specifies the assigned External and Private-TGW IP blocks, and determines which VPC Services can be enabled.
</div>

<div markdown>
![vCenter Conn Profile](images/3e-0-Conn_Prof.jpg){ width="100%" }
</div>

</div>

---

## Overview of Connectivity Profile Types

Different Connectivity Profile types are available:

| Type | Use Case | Routing Logic |
| :--- | :--- | :--- |
| [**Centralized Conn Prof**](#cent-conn) | Connectivity Profile for Centralized Transit Gateways. | VPC Gateways route traffic through a Centralized Transit Gateway. |
| [**Distributed Conn Prof**](#dist-conn)| Connectivity Profile for Distributed Transit Gateways. | VPC Gateways route traffic through a Distributed Transit Gateway. |

![Connectivity Prof Types](images/3e-0-Conn_Prof_Types.jpg){: .center style="width:75%" }


Defines the VPC's connection to the Transit Gateway, specifies the assigned External and Private-TGW IP blocks, and determines which VPC Services are enabled.


---

## Centralized Connectivity Profile {: #cent-conn }

![Conn Prof Cent](images/3e-1-Cent.jpg){: .center style="width:30%" }

### Configuration

#### Step1. Create Connectivity Profile
![Connectivity Prof config](images/3e-1a-Create_ConnProf.jpg){ width="95%" style="display: block; margin: 0 auto;" }

* **Transit Gateway**:  
  Select the Centralized Transit Gateway, VPC Gateways will be connected to.

* **External IP Blocks**:  
  Select the [External IP Block(s)](3c-ip_block.md#ext-ipblock) or create a new one for future VPC Subnets Public and NAT.
  
* **Private - Transit Gateway IP Blocks**:  
  Select the [Private IP Block(s)](3c-ip_block.md#privatetgw-ipblock) or create a new one for future VPC Subnets Private-TGW.
  
* **Edge Cluster**:  
  (Optional) Select the Edge Cluster to use for the VPC Gateway.  
  Required if N-S Services and/or Default Outbound NAT is enabled in this VPC Connectivity Profile.
  
* **N-S Services**:  
  Enable to allow [NAT](1c-vpc_nat.md#full-nat) on VPC Gateways.

* **Default Outbound NAT**:  
  Enable to allow [Default Outbound NAT](1c-vpc_nat.md#outbound-nat) on VPC Gateways.  
  Note: If the Centralized Transit Gateway selected is Active/Active, then Default Outbound NAT can not be enabled.

* **External IP Block for Default Outbound NAT**:  
  (Optional) Select a specif External IP Block to use for Outbound NAT.  
  If not selected, it will pick an IP from the External IP Blocks list configured above.



### Monitoring

#### Status
The status reflects the successful application of the configuration.

<div style="margin-left: 40px; margin-right: 40px;" markdown="1">
??? info "Note about the Status"
    Because this represents a logical configuration mapping rather than an active link-state protocol, the status will typically remain Green (Healthy) once the settings are validated by the NSX Manager.
</div>

![Connectivity Prof validation](images/3e-1b-Validation_ConProf.jpg){ width="80%" style="display: block; margin: 0 auto;" }


---

## Distributed Connectivity Profile {: #dist-conn }

![Conn Prof Dist](images/3e-2-Dist.jpg){: .center style="width:30%" }

### Configuration

#### Step1. Create Connectivity Profile
![Connectivity Prof config](images/3e-2a-Create_ConnProf.jpg){ width="95%" style="display: block; margin: 0 auto;" }

* **Transit Gateway**:  
  Select the Distributed Transit Gateway, VPC Gateways will be connected to.

* **External IP Blocks**:  
  Select the External IP Block(s) for future VPC Subnets Public and NAT.
  
* **Virtual Network Appliance Cluster**:  
  (Optional) Select the Virtual Network Appliance Cluster to use for the VPC Gateway.  
  Required if N-S Services and/or Default Outbound NAT is enabled in this VPC Connectivity Profile.
  
* **N-S Services**:  
  Enable to allow [NAT](1c-vpc_nat.md#full-nat) on VPC Gateways.

* **Default Outbound NAT**:  
  Enable to allow [Default Outbound NAT](1c-vpc_nat.md#outbound-nat) on VPC Gateways.  

* **External IP Block for Default Outbound NAT**:  
  Select the External IP Block to use for VPC Gateways which enabled Outbound NAT.


### Monitoring

#### Status
The status reflects the successful application of the configuration.

<div style="margin-left: 40px; margin-right: 40px;" markdown="1">
??? info "Note about the Status"
    Because this represents a logical configuration mapping rather than an active link-state protocol, the status will typically remain Green (Healthy) once the settings are validated by the NSX Manager.

![Connectivity Prof validation](images/3e-2b-Validation_ConProf.jpg){ width="80%" style="display: block; margin: 0 auto;" }
</div>

---
