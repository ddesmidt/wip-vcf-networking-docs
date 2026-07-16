<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **provisioning and managing Network Services within a VKS Namespace utilizing a "VDS + FLB" architecture"** inside a vSphere environment.

* **Network Services**
    * [**VM Load Balancers**](#networkservices)
</div>

<div markdown>
![VDS Architecture](images/1g1-0-lb.jpg){ width="100%" }
</div>
</div>

---

## Network Services: VM Load Balancers {: #networkservices }

The primary use case for configuring a **VM Load Balancer** is to efficiently distribute inbound network traffic across a designated pool of backend Virtual Machines.

![Topology](images/1g1-1-Topology.jpg){ width="80%" style="display: block; margin: 0 auto;" }

!!! warning "Current Limitation: No Application Health Checks"
    Currently, the vCenter Namespace VM Load Balancer does not perform application-level health checks on backend VMs.  
    If a backend VM goes down, the load balancer VIP will continue forwarding traffic to it.  
    This section will be updated once active health monitoring becomes officially available.

### Create a VM Load Balancer

In the "VDS + FLB" architecture", the load balancer service is only available via CLI.  
See [Deploy App (VMs)](1e2-deployment-vms.md#deployment_vms).


---

### Validate VM Load Balancer

See [Deploy App (VMs)](1e2-deployment-vms.md#deployment_vms).


