<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying an application (VMs/K8s) into the VKS Namespace with "NSX + DTGW/VNA"** within a vSphere environment.

* **Deployment App (VMs)**
    * [**via vCenter UI**](#deployment_vms)
    * [via yaml](2e2-deployment-vms.md)
* Deployment App (k8s)
    * [via vCenter UI](2f1-deployment-k8s.md)
    * [via yaml](2f2-deployment-k8s.md)
</div>

<div markdown>
![VDS Architecture](images/2e1-0-VM.jpg){ width="100%" }
</div>
</div>

---

## Deployment App (VMs) {: #deployment_vms }

### Deploy a VM via the vCenter UI

![Topology](images/2e1-1-Topology.jpg){ width="40%" style="display: block; margin: 0 auto;" }

Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Virtual Machine - Create VM**  
![Add Namespace Resources](images/2e1-1-namespace-resources.jpg){ width="95%" style="display: block; margin: 0 auto;" }

1. **Deploy VM from..** Select between **OVF** or **ISO**, and click **Next**.  
    ![Select VM Deployment Source](images/2e1-1a-VMFrom.jpg){ width="95%" align="center" }  

1. **Deploy a New VM** Choose a **VM Name**, a **VM Image**, a **VM Class** (size and reservation of the VM), and click **Review and Confirm**.  
    ![Configure New VM Settings](images/2e1-1b-NewVM.jpg){ width="95%" align="center" }  

1. **Review and Confirm** Review the settings, and click **Deploy VM**.  
    ![Review VM Deployment Details](images/2e1-1c-DeployVM.jpg){ width="95%" align="center" }  

    ??? info "More options"
        More options are available under **Advanced Settings (Optional)** and **Network Configuration (Optional)**, such as Persistent Volumes and Cloud-Init for Guest Customization.  
        For more information, refer to the [VMware VM Service Documentation](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-consumption/latest/vm-service.html){: target="_blank"}.

### Validate deployment and status of the VM  
You can validate the status of the VM from:

#### via vCenter Supervisor Namespace  
  Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Virtual Machine - Go to Service**
  ![View VM](images/2e1-1d-vm-info.jpg){ width="95%" style="display: block; margin: 0 auto;" }

#### via vCenter Inventory  
  Navigate to **vCenter** > **Inventory**, select the **VM in the Namespace**
  ![View VM](images/2e1-1e-vm-info.jpg){ width="95%" style="display: block; margin: 0 auto;" }


### Access the VM 
By default the VM is connected to a private network for security reasons.  
To offer direct access to the VM, differentes options sont possibles:  

* **Create a Subnet Public and plug the VM on it**  
  Under **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Network - Go to Service**  

    !!! warning "XXX PERSONAL Drafting Note"
        xxx Provide the steps xxx  



* **Create a Load Balancer in front of the VM**  
  Under **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Network - Go to Service**  
  
    !!! warning "XXX PERSONAL Drafting Note"
        xxx Provide the steps xxx  


