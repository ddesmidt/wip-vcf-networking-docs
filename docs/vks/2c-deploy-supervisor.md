<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying the Supervisor utilizing an "NSX + DTGW/VNA" architecture** inside a vSphere environment.

* [**Supervisor Deployment**](#supervisordeployment)

</div>

<div markdown>
![DTGW Architecture](images/0-DTGW.jpg){ width="100%" }
</div>
</div>

---

## Supervisor Deployment {: #supervisordeployment }

![Topology](images/2c-0-Topology.jpg){ width="80%" style="display: block; margin: 0 auto;" }

### Create Supervisor
Navigate to **vCenter** > **Supervisor Management**, and click **Get Started**.
![Add Supervisor Wizard](images/2c-1-AddSupervisor.jpg){ width="65%" style="display: block; margin: 0 auto;" }

1. **vCenter Server and Network**  
    * Select the network stack **VCF Networking with VPC (recommended)**, and click **Next**.  
    ![vCenter Server and Network Configuration](images/2c-1a-vCenter.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

2. **Supervisor Location**  
    * Select the **Cluster Deployment**, and click **Next**.  
    ![Supervisor Location Settings](images/2c-1b-Supervisor.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

3. **Storage**  
    * Select the different **Storage Policies**, and click **Next**.  
    ![Storage Policy Selection](images/2c-1c-Storage.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

4. **Management Network**  
    * Configure the **Supervisor Management IP Settings**, and click **Next**.  
    ![Management Network IP Settings](images/2c-1d-Management.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

5. **Workload Network**  
    * Configure the **Workload Network** (fields are pre-populated, except for DNS and NTP Servers), and click **Next**.  
    ![Workload Network Configuration](images/2c-1e-Workload.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

        ??? warning "Troubleshooting: Auto SNAT Error"
            If you receive the error *"Auto SNAT must be enabled for VPC Connectivity Profile Default"*, refer back to the **["DTGW + VNA" Requirements](2a-requirements.md#nsx)** page and ensure **Default Outbound NAT** is enabled in the Connectivity Profile.

6. **Advanced Settings**  
    * Select the **Supervisor Control Plane Size**, and click **Next**.  
    ![Advanced Settings and Sizing](images/2c-1f-Advanced.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

7. **Ready to Complete**  
    * Review your configuration and click **Finish**.  
    ![Review and Complete](images/2c-1g-Ready.jpg){ width="95%" style="display: block; margin: 0 auto;" }  


---

### Finish Supervisor Creation for future Kubernetes Clusters

#### **Create a Content Library for future Kubernetes Clusters**  
If you plan to deploy Kubernetes Clusters, create a Content Library with VKS images.  
Navigate to **vCenter** > **Content Libraries** > and click **Create**.  
    ![vCenter Server and Network Configuration](images/2c-2a-ContentLibrary.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

  1. **Name and Location**  
    Give it a **Name** and select the **vCenter** hosting that Content Library, and click **Next**.  
    ![vCenter Server and Network Configuration](images/2c-3a-Name.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  2. **Configure Content Library**  
    Choose between **Local content library** (you manually upload VKS images) and **Subscribed content library** (vCenter automatically downloads VKS images from a repository), and click **Next**.  
    > *Note: This example uses a Subscribed content library pointing to the public repository `https://wp-content.vmware.com/v2/latest/lib.json`, with the option to "download content when needed" to save local storage space.*  
  
    ![vCenter Server and Network Configuration](images/2c-3b-ContentLibrary.jpg){ width="85%" style="display: block; margin: 0 auto;" }   

  3. **Apply Security Policy**  
    Apply a **security policy** if required by your organization, and click **Next**.  
    *(This example uses no security policy).*  
    ![vCenter Server and Network Configuration](images/2c-3c-SecurityPolicy.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  4. **Add Storage**  
    Select a **storage** to host the content library images, and click **Next**.  
    ![vCenter Server and Network Configuration](images/2c-3d-Storage.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  5. **Ready to complete**  
    Review the content library, and click **Finish**.  
    ![vCenter Server and Network Configuration](images/2c-3e-Review.jpg){ width="85%" style="display: block; margin: 0 auto;" }  


#### **Associate the Content Library to the Supervisor**  
Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, and click on **Kubernetes Service - Manage**.
    ![vCenter Server and Network Configuration](images/2c-4a-Supervisor.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

  1. **Add Content Library**  
    Click **ADD**.  
    ![vCenter Server and Network Configuration](images/2c-4b-AddContentLibrary.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  1. **Select Content Library**  
    Select **Content Library with VKS images**, and click **ADD**.  
    ![vCenter Server and Network Configuration](images/2c-4c-SelectContentLibrary.jpg){ width="65%" style="display: block; margin: 0 auto;" }  

---

### Validate Deployment

#### **Validate Supervisor Status**  
Once the wizard completes, verify the deployment was successful by navigating to **vCenter** > **Supervisor Management** > **Supervisors**. 

Check the following fields to ensure they reflect a healthy state:
<ul style="margin-top: -10px; margin-bottom: 15px; line-height: 1.3;">
  <li style="margin-bottom: 2px;">Config Status</li>
  <li style="margin-bottom: 2px;">Host Config Status</li>
  <li style="margin-bottom: 2px;">Control Plane Node Address</li>
</ul>
![Supervisor Validation Status](images/2c-5a-Validation.jpg){ width="95%" style="display: block; margin: 0 auto;" }

#### **Validate Supervisor Content Library**  
Validate Supervisor Content Library by navigating to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**
![Supervisor Validation Status](images/2c-5b-Validation.jpg){ width="85%" style="display: block; margin: 0 auto;" }

