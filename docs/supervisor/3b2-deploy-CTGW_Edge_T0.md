<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + CTGW/Edge/T0"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the requirements for **deploying the Supervisor utilizing an "NSX + CTGW/Edge/T0" architecture** inside a vSphere environment.

* [Requirements](3a-requirements.md)
* **Install Requirements**
    * [NSX Overlay](3b1-deploy-NSXOverlay.md)
    * [**CTGW + Edge + T0**](#installation)

</div>

<div markdown>
![CTGW Architecture](images/0-CTGW.jpg){ width="100%" }
</div>
</div>

---

## Install Requirements - CTGW + Edge + T0 {: #installation }

When no Transit Gateway has been deployed, NSX offers a simple wizard to deploy the CTGW + Edge + T0.

![Topology](images/3b2-0-Topology.jpg){ width="80%" style="display: block; margin: 0 auto;" }

### Launch "Setup Network Connectivity"

Navigate to **NSX** > **Networking** > **VPC Connectivity** > **Transit Gateways**.  
![Setup Network Connectivity Wizard](images/2b2-0-SetupNetworkConnectivity.jpg){ width="95%" style="display: block; margin: 0 auto;" }


<div style="margin-left: 20px; margin-right: 20px;" markdown="1">
!!! info "Deep Dive: Blogs & Video Demonstrations"
    * **Video Walkthrough:** Watch the step-by-step CTGW + Edge + T0 deployment [<span style="color: #FF0000;">:fontawesome-brands-youtube:</span> on YouTube](https://www.youtube.com/watch?v=ruoprGnf_v8){ target="_blank" }.
    * **Technical Blog:** Read the detailed architectural breakdown on the [<span style="color: #007cbb;">:material-newspaper-variant-outline:</span> VMware Cloud Foundation Blog](https://blogs.vmware.com/cloud-foundation/2025/06/25/vpc-centralized-network-connectivity-with-guided-edge-deployment/){ target="_blank" }.
</div>

---

### Validate Deployment
Once the wizard completes, verify the deployment was successful using the steps outlined in the prerequisites:  
**[Validate "CTGW + Edge + T0" Deployment](3a-requirements.md#ctgw-edge-t0-ready)**