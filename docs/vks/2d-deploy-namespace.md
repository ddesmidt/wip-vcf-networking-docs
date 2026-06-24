<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying the VKS Supervisor with "NSX + DTGW/VNA"** within a vSphere environment.

* [Requirements](2a-requirements.md)
* [Supervisor Deployment](2b-deploy-supervisor.md)
* [**Namespace Deployment**](#namespacedeployment)
</div>

<div markdown>
![VDS Architecture](images/0-DTGW.jpg){ width="100%" }
</div>
</div>

---

## Namespace Deployment {: #namespacedeployment }

### Create NameSpace
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


#### **Create a Content Library for future VMs**  
If you plan to deploy VMs, create a Content Library with VM images.  
Navigate to **vCenter** > **Content Libraries** > and click **Create**.  
![vCenter Server and Network Configuration](images/2b-2a-ContentLibrary.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

  1. **Name and Location**  
    Give it a **Name** and select the **vCenter** hosting that Content Library, and click **Next**.  
    ![vCenter Server and Network Configuration](images/2b-2a-Name.jpg){ width="85%" style="display: block; margin: 0 auto;" }  
    
  1. **Configure Content Library**  
    Choose between **Local content library** (you upload VM images) and **Subscribed content library** (vCenter downloads VM images from a repository), and click **Next**.  
    *I'm using here Local content library.*  
    ![vCenter Server and Network Configuration](images/2b-2b-ContentLibrary.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  1. **Apply Security Policy**  
    Apply **security policy** if you choose to, and click **Next**.  
    *I'm using here no security policy.*  
    ![vCenter Server and Network Configuration](images/2b-2c-SecurityPolicy.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  1. **Add Storage**  
    Select a **storage** to host the content library images, and click **Next**.  
    ![vCenter Server and Network Configuration](images/2b-2d-Storage.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  1. **Ready to complete**  
    Review the content library, and click **Finish**.  
    ![vCenter Server and Network Configuration](images/2b-2e-Review.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  1. **Import VM Image**  
    In the Content Library created, import a VM Image, click on **Import Item**.  
    ![vCenter Server and Network Configuration](images/2b-2f-ImportItem.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

  1. **Choose VM Image**  
    Choose a **Source file** via URL or Local file, and click **Import**.  
    *I'm using here the URL https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.ova to download Ubuntu 24.04.*  
    ![vCenter Server and Network Configuration](images/2b-2g-ImportURL.jpg){ width="85%" style="display: block; margin: 0 auto;" }  
