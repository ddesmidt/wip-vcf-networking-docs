<h1>
  <img src="../../assets/vCenter.png" style="height:30px"; vertical-align:middle;> VPC Gateway Configuration in vCenter
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for configuring a VPC Gateway using the vSphere Client.
<br><br>
**VPC Gateways** are the logical router for VPC networking.
</div>

<div markdown>
![vCenter VPC Connectivity](images/1a-0-VPC_Gateway_Info.jpg){ width="100%" }
</div>

</div>

---

## VPC Gateway

### Configuration

#### Step1. Create new VPC Gateway
![vCenter Create VPC](images/1a-1-Create_VPC_Gateway.jpg){ width="80%" style="display: block; margin: 0 auto;" }

#### Step2. Configure new VPC Gateway
![vCenter Create VPC](images/1a-2-Create_VPC_Gateway.jpg){ width="50%" style="display: block; margin: 0 auto;" }

* **Private - VPC IP CIDRs**  
  (Optional) Defines the IP address space reserved for internal VPC subnets.  
  Address selection here is highly flexible; since all traffic from Private VPC subnets is **Source NATed (SNATed)** using a Public/External IP before exiting the VPC, these internal addresses can not conflict with your existing physical network infrastructure.

* **Connectivity Profile**  
  Select the pre-defined [Connectivity Profile](3e-connectivity_profile.md).  
  This profile links the VPC to:  
  . [Transit Gateway](3b-transit_gateway.md) - determines the primary North-South path to the physical network  
  . [External IP Blocks](3c-ip_block.md) and [Private -TGW IP Blocks](3c-ip_block.md) - determines the future IP addressing of the VPC subnets / NAT / LB  
  . [Default Outbound SNAT](1c-vpc_nat.md) - determines if compute (VMs / VKS) connected to Private VPC subnets will be SNATed by default to access external networks  
  Note: The Network Span (which vCenter Cluster(s) can access this VPC) is configured at the Transit Gateway level.

* **Connectivity Policy**  
  Select the [Connectivity Policy](3f-connectivity_policy.md) to govern East/West traffic.  
  This determines whether communication is permitted or denied between this VPC and other VPC subnets within the environment.

### Monitoring
#### Topology
You can see the VPC Gateway in a graphical way under Topology:
![vCenter Validation VPC Gateway](images/1a-3-Validation_VPC_Gateway.jpg){ width="90%" style="display: block; margin: 0 auto;" }

---
