<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **provisioning and managing Network Services within a VKS Namespace utilizing an "NSX + DTGW/VNA"** architecture inside a vSphere environment.

* **Network Services**
    * [**Subnets**](#networkservices)
    * [SubnetSets](2h2-network-subnetset.md)
    * [Static Routes](2h3-network-staticroute.md)
    * [External IPs](2h4-network-externalip.md)
    * [VM Load Balancers](2h5-network-lb.md)

</div>

<div markdown>
![VDS Architecture](images/2h1-0-subnet.jpg){ width="100%" }
</div>
</div>

---

## Network Services - Subnets {: #networkservices }

![Topology](images/2h1-1-Topology.jpg){ width="55%" style="display: block; margin: 0 auto;" }

### Create Subnet

Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select your target Supervisor, click the **Namespaces** tab, and select your specific Namespace.  
Under the **Resources** card, click **Network - Go to Service**.  
![Network Service](images/2h1-1-network.jpg){ width="95%" style="display: block; margin: 0 auto;" }

1. **Create New Subnet**  
Navigate to **Subnets**, and click **New Subnet**.  
![Create Subnet](images/2h1-1a-subnetcreate.jpg){ width="70%" style="display: block; margin: 0 auto;" }  

For more information about Subnets settings review the [VPC Subnet Overlay page](../../vcenter/1b-vpc_subnet/#overlay){target="_blank"}.


---

### Validate Subnet Creation
Expand the newly created Subnet to view its assigned CIDR and VPC Gateway.
![Created Subnet](images/2h1-2a-subnetcreated.jpg){ width="90%" style="display: block; margin: 0 auto;" }  

