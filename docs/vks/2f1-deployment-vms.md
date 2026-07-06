<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying an application (VMs/K8s) into the VKS Namespace with "NSX + DTGW/VNA"** within a vSphere environment.

* **Deploy App (VMs)**
    * [**via vCenter UI**](#deployment_vms)
    * [via CLI](2f2-deployment-vms.md)
* Deploy App (K8s)
    * [via CLI](2g1-deployment-pods.md)
</div>

<div markdown>
![VDS Architecture](images/2f1-0-VM.jpg){ width="100%" }
</div>
</div>

---

## Deploy App (VMs) {: #deployment_vms }

![Topology](images/2f1-1-Topology.jpg){ width="55%" style="display: block; margin: 0 auto;" }

### Deploy a VM

Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Virtual Machine - Create VM**  
![Add Namespace Resources](images/2f1-1-namespace-resources.jpg){ width="95%" style="display: block; margin: 0 auto;" }

1. **Deploy VM from..**  
Select between **OVF** or **ISO**, and click **Next**.  
![Select VM Deployment Source](images/2f1-1a-VMFrom.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Deploy a New VM**  
Choose a **VM Name**, a **VM Image**, a **VM Class** (size and reservation of the VM), and click **Review and Confirm**.  
![Configure New VM Settings](images/2f1-1b-NewVM.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Review and Confirm**  
Review the settings, and click **Deploy VM**.  
![Review VM Deployment Details](images/2f1-1c-DeployVM.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

    ??? info "More options"
        More options are available under **Advanced Settings (Optional)** and **Network Configuration (Optional)**, such as Persistent Volumes and Cloud-Init for Guest Customization.  
        For more information, refer to the [VMware VM Service Documentation](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-consumption/latest/vm-service.html){: target="_blank"}.

---

### Validate deployment and status of the VM  

#### via vCenter Supervisor Namespace  
Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Virtual Machine - Go to Service**
![View VM](images/2f1-1d-vm-info.jpg){ width="95%" style="display: block; margin: 0 auto;" }

#### via vCenter Inventory  
Navigate to **vCenter** > **Inventory**, select the **VM in the Namespace**.
![View VM](images/2f1-1e-vm-info.jpg){ width="95%" style="display: block; margin: 0 auto;" }


---

### Access the VM 
By default the VM is connected to the Private-VPC subnetset `vm-default`.  
To offer direct access to the VM, there are several options:

* **Create a Public Subnet and plug the VM on it**  
Under **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Network - Go to Service**  

    !!! warning "XXX PERSONAL Drafting Note"
        xxx Provide the steps xxx  

* **Create a Load Balancer in front of the VM**  
Under **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, navigate to **Resources**, and click on **Network - Go to Service**  
  
    !!! warning "XXX PERSONAL Drafting Note"
        xxx Provide the steps xxx  


