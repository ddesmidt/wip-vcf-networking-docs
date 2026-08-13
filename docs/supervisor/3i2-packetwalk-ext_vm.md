<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + CTGW/Edge/T0"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **Troubleshooting Network Services into the VKS Namespace utilizing an "NSX + CTGW/Edge/T0" architecture** inside a vSphere environment.

* **Packet Walk**  
    * [N/S External to VIP](3i1-packetwalk-ext_vip.md)  
    * [**N/S External to VM**](#packetwalk)  
    * [E/W Pod to Pod](3i3-packetwalk-pod_pod.md)  
    * [E/W VM to VM](3i4-packetwalk-vm_vm.md)  
* App Access Broken  
    * [VIP aAccess Down](3j1-troubleshooting-vip.md)  
    * [VM Access Down](3j2-troubleshooting-vm.md)  
    * [Pod Access Down](3j3-troubleshooting-pod.md)  

</div>

<div markdown>
![VDS Architecture](images/3f1-0-VM.jpg){ width="100%" }
</div>
</div>

---

## Packet Walk - N/S External to VM {: #packetwalk }

One or a few VMs have been deployed (see [Application Deployment > App Deployment (VMs) > via vCenter UI](2f1-deployment-vms.md#deployment_vms) or [Application Deployment > App Deployment (VMs) > via CLI](2f2-deployment-vms.md#deployment_vms)).

### View

#### Logical View
* **Use case: VM connected to Private Subnet (Private-VPC or Private-TGW)**  
![Logical](images/2i2-0-LogicalView1.jpg){ width="75%" style="display: block; margin: 0 auto;" }
In this case, external clients can't reach that subnet, and so can't reach VMs on it.

* **Use case: VM connected to Public Subnet**  
![Logical](images/2i2-0-LogicalView2.jpg){ width="75%" style="display: block; margin: 0 auto;" }

#### Physical View
* **Use case: VM connected to Public Subnet**  
![Physical](images/2i2-0-PhysicalView2.jpg){ width="95%" style="display: block; margin: 0 auto;" }

---

### Packet Walk

* **Step1: External Client accesses the VM**  
`Client-IP => VM (10.1.7.146)`  

    The traffic enters the ESX hosting the VM.

