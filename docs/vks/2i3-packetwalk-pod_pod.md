<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **Troubleshooting Network Services into the VKS Namespace utilizing an "NSX + DTGW/VNA" architecture** inside a vSphere environment.

* **Packet Walk** 
    * [N/S External to VIP](2i1-packetwalk-ext_vip.md)  
    * [N/S External to VM](2i2-packetwalk-ext_vm.md)  
    * [**E/W Pod to Pod**](#packetwalk)  
    * [E/W VM to VM](2i4-packetwalk-vm_vm.md)  
* **App Access broken(ToDO)**
    * [VIP access down](2j1-troubleshooting-vip.md)  
    * [VM access down](2j2-troubleshooting-vm.md)  
    * [Pod access down](2j3-troubleshooting-pod.md)  

</div>

<div markdown>
![VDS Architecture](images/2g1-0-k8s.jpg){ width="100%" }
</div>
</div>

---

## Packet Walk - E/W pod to pod {: #packetwalk }

A Full Application (Load Balancer + Pods) has been deployed (see [Deploy App (K8s) > via CLI](2g1-deployment-pods.md#deployment_pods)).

### View

#### Logical View
![Logical](images/2i3-0-LogicalView.jpg){ width="75%" style="display: block; margin: 0 auto;" }

#### Physical View
![Physical](images/2i3-0-PhysicalView.jpg){ width="95%" style="display: block; margin: 0 auto;" }

---

### Packet Walk Explanation

* **Step 1: Traffic leaves the Source Pod**  
`Source-Pod-IP (192.168.147.3) => Destination-Pod-IP (192.168.148.3)`  

    The traffic exits the source pod and hits the Worker Node's local routing engine (managed by `kube-proxy` and the CNI agent, such as Antrea).  

* **Step 2: Cross-Node Encapsulation**  
```text
WorkerNode1-IP (172.30.0.5) => WorkerNode2-IP (172.30.0.6)
  [Source-Pod-IP (192.168.147.3) => Destination-Pod-IP (192.168.148.3)]
```
    The destination pod is on a different node, so the Worker Node encapsulates the original packet inside a node-to-node tunnel packet.  

    > **Note:** If the Worker Nodes reside on a different ESX, the cross-ESX traffic is encapsulated using the ESX TEP IPs.  
    ```text
    ESX1-TEP_IP (10.1.3.203) => ESX2-TEP_IP (10.1.3.207)
      [WorkerNode1-IP (172.30.0.5) => WorkerNode2-IP (172.30.0.6)]
        [[Source-Pod-IP (192.168.147.3) => Destination-Pod-IP (192.168.148.3)]]
    ```



* **Step 3: Destination Node Decapsulation and Delivery (encapsulation2)**    
    `Source-Pod-IP (192.168.147.3) => Destination-Pod-IP (192.168.148.3)`

    The destination Worker Node receives the node-to-node packet and strips away the encapsulation header. The local routing rules then deliver the unencapsulated packet directly into the destination Pod's network interface.