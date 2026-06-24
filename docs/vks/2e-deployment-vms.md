<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying the VKS Supervisor with "NSX + DTGW/VNA"** within a vSphere environment.

* [Requirements](2a-requirements.md)
* [Supervisor Deployment](2b-deployment.md)
* [**Deployment App (VMs)**](#deployment_vms)
* [Deployment App (k8s)](2e-deployment-k8s.md)
</div>

<div markdown>
![VDS Architecture](images/0-DTGW.jpg){ width="100%" }
</div>
</div>

---

## Deployment App (VMs) {: #deployment_vms }

![Topology](images/2c-0-Topology.jpg){ width="80%" style="display: block; margin: 0 auto;" }

### Create Supervisor NameSpace
Navigate to **vCenter** > **Supervisor Management** > **Namespces**, and click **NEW NAMESPACE**.
![Add Namespace](images/2c-1-AddSupervisor.jpg){ width="95%" style="display: block; margin: 0 auto;" }

1. **Location**  
    * Select the **Supervisor**, and click **Next**.  
    ![vCenter Server and Network Configuration](images/2c-1a-SupervisorNamespace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Configuration**  
    * Give a name to the **Namespace**, and click **Next**.  
    ![vCenter Server and Network Configuration](images/2c-1b-SupervisorNamespace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Add Zones**  
    * Select the **Workload Zone** (one or more vCenter Clusters), and click **Next**.  
    ![vCenter Server and Network Configuration](images/2c-1c-SupervisorNamespace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Review**  
    * Review the Namespace settings, and click **Finish**.  
    ![vCenter Server and Network Configuration](images/2c-1d-SupervisorNamespace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

###  Supervisor NameSpace to be ready to host workloads (VMs and Containers)
Navigate to **vCenter** > **Supervisor Management** > **Namespces**, and click **NEW NAMESPACE**.
![Add Namespace](images/2c-1-AddSupervisor.jpg){ width="95%" style="display: block; margin: 0 auto;" }
