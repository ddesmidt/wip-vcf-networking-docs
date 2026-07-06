<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying a Supervisor Namespace utilizing an "NSX + DTGW/VNA" architecture** inside a vSphere environment.

* **Namespace**
    * [**Deployment**](#namespacedeployment)
    * [Accesss via CLI](2d2-access-namespace.md)

</div>

<div markdown>
![VDS Architecture](images/2d1-0-Namespace.jpg){ width="100%" }
</div>
</div>

---

## Namespace Deployment {: #namespacedeployment }

![Topology](images/2d1-0-Topology.jpg){ width="80%" style="display: block; margin: 0 auto;" }


### Create Namespace
Navigate to **vCenter** > **Supervisor Management** > **Namespaces**, and click **NEW NAMESPACE**.
![Add Namespace](images/2c-1-AddSupervisor.jpg){ width="95%" style="display: block; margin: 0 auto;" }

1. **Location** Select the **Supervisor**, and click **Next**.  
    ![Select Supervisor Location](images/2d1-1a-SupervisorNamespace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Configuration** Give a name to the **Namespace**, and click **Next**.  
    ![Name the Namespace](images/2d1-1b-SupervisorNamespace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Add Zones** Select the **Workload Zone** (one or more vCenter Clusters), and click **Next**.  
    ![Add Workload Zones](images/2d1-1c-SupervisorNamespace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Review** Review the Namespace settings, and click **Finish**.  
    ![Review Namespace Settings](images/2d1-1d-SupervisorNamespace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  



---

### Finish Namespace Creation for future Applications

#### **Create a Content Library for future VMs**  
If you plan to deploy VMs, create a Content Library with VM images.  
Navigate to **vCenter** > **Content Libraries** > and click **Create**.  
![Create Content Library](images/2c-2a-ContentLibrary.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Name and Location** Give it a **Name** and select the **vCenter** hosting that Content Library, and click **Next**.  
    ![Name Content Library](images/2d1-2b-Name.jpg){ width="85%" style="display: block; margin: 0 auto;" }  
    
1. **Configure Content Library** Choose between **Local content library** (you upload VM images) and **Subscribed content library** (vCenter downloads VM images from a repository), and click **Next**.  
    *I'm using here Local content library.* ![Configure Content Library Type](images/2d1-2c-ContentLibrary.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

1. **Apply Security Policy** Apply **security policy** if you choose to, and click **Next**.  
    *I'm using here no security policy.* ![Apply Security Policy](images/2d1-2d-SecurityPolicy.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

1. **Add Storage** Select a **storage** to host the content library images, and click **Next**.  
    ![Select Storage](images/2d1-2e-Storage.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

1. **Ready to complete** Review the content library, and click **Finish**.  
    ![Review Content Library Configuration](images/2d1-2f-Review.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

1. **Import VM Image** In the Content Library created, import a VM Image, click on **Import Item**.  
    ![Import VM Image Item](images/2d1-2g-ImportItem.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

1. **Choose VM Image** Choose a **Source file** via URL or Local file, and click **Import**.  
    *I'm using here the URL https://cloud-images.ubuntu.com/noble/current/noble-server-cloudimg-amd64.ova to download Ubuntu 24.04.* ![Import VM Image URL](images/2d1-2h-ImportURL.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

#### **Associate the Content Library to the Namespace**  
Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, and select **[your namespace]**, and click on **VM Service - Add Content Library**.
![Navigate to Namespace Content Library](images/2d1-3a-NameSpace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Add Content Library** Select **Content Library with VM images**, and click **OK**.  
    ![Select Content Library](images/2d1-3b-AddContentLibrary.jpg){ width="85%" style="display: block; margin: 0 auto;" }  

#### **Associate Storage Policy to the Namespace**
Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, and select **[your namespace]**, and click on **Storage - Add Storage**.
![Navigate to Namespace Storage](images/2d1-4a-NameSpace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Add Storage Policy** Select **Storage Policy**, and click **OK**.  
    ![Select Storage Policy](images/2d1-4b-AddStorage.jpg){ width="85%" style="display: block; margin: 0 auto;" }  


#### **Associate VM Class to the Namespace**  
Navigate to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, and select **[your namespace]**, and click on **VM Service - Add VM Class**.
![Navigate to Namespace VM Class](images/2d1-3a-NameSpace.jpg){ width="95%" style="display: block; margin: 0 auto;" }  

1. **Add VM Classes** Select **VM Classes**, and click **OK**.  
*I selected here all VM Classes type "small" and "medium".*
![Add VM Classes](images/2d1-5b-AddVMClass.jpg){ width="85%" style="display: block; margin: 0 auto;" }  



---

### Validate Deployment

#### **Validate Namespace Status** 
Once the wizard completes, verify the deployment was successful by navigating to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, and navigate to **Namespaces**.

![Validate Namespace Status](images/2d1-6a-Validation.jpg){ width="85%" style="display: block; margin: 0 auto;" }

#### **Validate Namespace Content Library** 
Verify the Namespace has at least a **Content Library**, **VM Classes**, and **Storage** by navigating to **vCenter** > **Supervisor Management** > **Supervisors**, select **[your supervisor]**, navigate to **Namespaces**, and select **[your namespace]**.
![Validate Namespace Resources](images/2d1-6b-Validation.jpg){ width="85%" style="display: block; margin: 0 auto;" }


