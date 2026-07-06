<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **provisioning and managing Network Services within a VKS Namespace utilizing an "NSX + DTGW/VNA"** architecture inside a vSphere environment.

* **Network Services**
    * [Subnets](2h1-network-subnet.md)
    * [**SubnetSets**](#networkservices)
    * [Static Routes](2h3-network-staticroute.md)
    * [External IPs(ToDO)](2h4-network-externalip.md)
    * [VM Load Balancers(ToDO)](2h5-network-lb.md)

</div>

<div markdown>
![VDS Architecture](images/2h2-0-subnetset.jpg){ width="100%" }
</div>
</div>

---

## Network Services - Subnetsets {: #networkservices }

![Topology](images/2h2-1-Topology.jpg){ width="55%" style="display: block; margin: 0 auto;" }

### Create SubnetSet

Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Network - Go to Service**  
![Add Namespace Resources](images/2h1-1-network.jpg){ width="95%" style="display: block; margin: 0 auto;" }

1. **Create New SubnetSet**  
Navigate to **SubnetSets**, and click **New SubnetSet**  
![Create Subnet](images/2h2-1a-subnetsetcreate.jpg){ width="50%" style="display: block; margin: 0 auto;" }  

A **SubnetSet** provides dynamic, automated scaling functionality for subnets:  

* **Scale Out**: As soon as a subnet within the SubnetSet consumes its available IP capacity, a new subnet is automatically provisioned.  
* **Scale In**: Conversely, when a subnet becomes completely empty (no workload attached to it), it is automatically deleted.

For more information about Subnets settings review the [VPC Subnet Overlay page](../../vcenter/1b-vpc_subnet/#overlay){target="_blank"}.


