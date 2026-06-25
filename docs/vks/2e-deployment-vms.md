<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying an application (VMs/K8s) into the VKS Namespace with "NSX + DTGW/VNA"** within a vSphere environment.

* [**Deployment App (VMs)**](2e-deployment-vms.md#deployment_vms)
* [Deployment App (k8s)](2f-deployment-k8s.md)
</div>

<div markdown>
![VDS Architecture](images/0-DTGW.jpg){ width="100%" }
</div>
</div>

---

## Deployment App (VMs) {: #deployment_vms }

![Topology](images/2e-0-Topology.jpg){ width="80%" style="display: block; margin: 0 auto;" }

### Deploy a VM via the vCenter UI
Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, select **[your namespace]**, and navigate to **Resources**.  
![Add Namespace Resources](images/2e-1-namespace-resources.jpg){ width="95%" style="display: block; margin: 0 auto;" }

1. **Deploy VM from..** Select between **OVF** or **ISO**, and click **Next**.  
    ![Select VM Deployment Source](images/2e-1a-VMFrom.jpg){ width="95%" align="center" }  

1. **Deploy a New VM** Choose a **VM Name**, a **VM Image**, a **VM Class** (size and reservation of the VM), and click **Review and Confirm**.  
    ![Configure New VM Settings](images/2e-1b-NewVM.jpg){ width="95%" align="center" }  

1. **Review and Confirm** Review the settings, and click **Deploy VM**.  
    ![Review VM Deployment Details](images/2e-1c-DeployVM.jpg){ width="95%" align="center" }  

    ??? info "More options"
        More options are available under **Advanced Settings (Optional)** and **Network Configuration (Optional)**, such as Persistent Volumes and Cloud-Init for Guest Customization.  
        For more information, refer to the [VMware VM Service Documentation](https://techdocs.broadcom.com/us/en/vmware-cis/vcf/vcf-consumption/latest/vm-service.html).


### Deploy a Full Application (Load Balancer + VMs) via yaml file

1. **Connect to Supervisor Namespace via kubectl**  
    See [Connect to Namespace via Kubectl client](2d2-access-namespace.md#namespacek8sclient)




