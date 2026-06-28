<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying an application (VMs/K8s) into the VKS Namespace with "NSX + DTGW/VNA"** within a vSphere environment.

* Deployment App (VMs)
    * [via vCenter UI](2f1-deployment-vms.md)
    * [via CLI](2f2-deployment-vms.md)
* **Deployment App (k8s)**
    * [via vCenter UI](2g1-deployment-pods.md)
    * [**via CLI**](#deployment_pods)
</div>

<div markdown>
![VDS Architecture](images/2g1-0-k8s.jpg){ width="100%" }
</div>
</div>

---

## Deployment App (k8s) {: #deployment_pods }

### Deploy a Full Application (Load Balancer + VMs) via CLI


