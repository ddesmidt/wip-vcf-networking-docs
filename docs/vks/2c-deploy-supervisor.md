<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying the VKS Supervisor with "NSX + DTGW/VNA"** within a vSphere environment.

* [Requirements](2a-requirements.md)
* [**Supervisor Deployment**](#supervisordeployment)
* [Namespace Deployment](2c-deploy-namespace.md)
</div>

<div markdown>
![DTGW Architecture](images/0-DTGW.jpg){ width="100%" }
</div>
</div>

---

## Supervisor Deployment {: #supervisordeployment }

![Topology](images/2b-0-Topology.jpg){ width="80%" style="display: block; margin: 0 auto;" }

### Launch "Supervisor Creation Wizard"
Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, and click **ADD SUPERVISOR**.
![Add Supervisor Wizard](images/2b-1-AddSupervisor.jpg){ width="95%" style="display: block; margin: 0 auto;" }

1. **vCenter Server and Network**  
    * Select the network stack **VCF Networking with VPC (recommended)**, and click **Next**.  
    ![vCenter Server and Network Configuration](images/2b-1a-vCenter.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Supervisor Location**  
    * Select the **Cluster Deployment**, and click **Next**.  
    ![Supervisor Location Settings](images/2b-1b-Supervisor.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Storage**  
    * Select the different **Storage Policies**, and click **Next**.  
    ![Storage Policy Selection](images/2b-1c-Storage.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Management Network**  
    * Configure the **Supervisor Management IP Settings**, and click **Next**.  
    ![Management Network IP Settings](images/2b-1d-Management.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Workload Network**  
    * Configure the **Workload Network** (fields are pre-populated, except for DNS and NTP Servers), and click **Next**.  
    ![Workload Network Configuration](images/2b-1e-Workload.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

        ??? warning "Troubleshooting: Auto SNAT Error"
            If you receive the error *"Auto SNAT must be enabled for VPC Connectivity Profile Default"*, refer back to the **["DTGW + VNA" Requirements](2a-requirements.md#nsx)** page and ensure **Default Outbound NAT** is enabled in the Connectivity Profile.

1. **Advanced Settings**  
    * Select the **Supervisor Control Plane Size**, and click **Next**.  
    ![Advanced Settings and Sizing](images/2b-1f-Advanced.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Ready to Complete**  
    * Review your configuration and click **Finish**.  
    ![Review and Complete](images/2b-1g-Ready.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

---

### Validate Deployment
Once the wizard completes, verify the deployment was successful by navigating to **vCenter** > **Supervisor Management** > **Supervisors**. 

Check the following fields to ensure they reflect a healthy state:
<ul style="margin-top: -10px; margin-bottom: 15px; line-height: 1.3;">
  <li style="margin-bottom: 2px;">Config Status</li>
  <li style="margin-bottom: 2px;">Host Config Status</li>
  <li style="margin-bottom: 2px;">Control Plane Node Address</li>
</ul>

![Supervisor Validation Status](images/2b-2-Validation.jpg){ width="95%" style="display: block; margin: 0 auto;" }

