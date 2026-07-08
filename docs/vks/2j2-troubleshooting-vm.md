<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **Troubleshooting Network Services into the VKS Namespace utilizing an "NSX + DTGW/VNA" architecture** inside a vSphere environment.

* Packet Walk  
    * [N/S External to VIP](2i1-packetwalk-ext_vip.md)  
    * [N/S External to VM](2i2-packetwalk-ext_vm.md)  
    * [E/W pod to pod](2i3-packetwalk-pod_pod.md)  
    * [E/W VM to VM](2i4-packetwalk-vm_vm.md)  
* **App Access broken**  
    * [VIP access down](2j1-troubleshooting-vip.md)  
    * [**VM access down**](#troubleshooting)  
    * [Pod access down](2j3-troubleshooting-pod.md)  

</div>

<div markdown>
![VDS Architecture](images/2f1-0-VM.jpg){ width="100%" }
</div>
</div>

---

## Troubleshooting - VM access down {: #troubleshooting }

As described in the [Packet Walk - N/S External to VM](2i2-packetwalk-ext_vm.md) section, clients accessing a VM traverse the following path:

#### Logical View
* **Use case: VM connected to Private Subnet (Private-VPC or Private-TGW)**  
![Logical](images/2i2-0-LogicalView1.jpg){ width="75%" style="display: block; margin: 0 auto;" }
In this case, external clients can't reach that subnet, and so can't reach VMs on it.

* **Use case: VM connected to Public Subnet**  
![Logical](images/2i2-0-LogicalView2.jpg){ width="75%" style="display: block; margin: 0 auto;" }

#### Physical View
* **Use case: VM connected to Public Subnet**  
![Physical](images/2i2-0-PhysicalView2.jpg){ width="95%" style="display: block; margin: 0 auto;" }


### Step1: Physical fabric to the ESX Node hosting the VIP
* **Validate communication to the VM**  
    Validate IP reachability to the VIP with a small and large `ping`.  

    ??? info "Status Validation"
        <pre><code>PS C:\Users\Administrator\Documents> <b>ping 10.1.7.146</b>
        Pinging 10.1.7.146 with 32 bytes of data:
        Reply from 10.1.7.146: bytes=32 time=6ms TTL=61
        Reply from 10.1.7.146: bytes=32 time=1ms TTL=61

        PS C:\Users\Administrator\Documents> <b>ping 10.1.7.146 -l 1400</b>
        Pinging 10.1.7.146 with 1400 bytes of data:
        Reply from 10.1.7.146: bytes=1400 time=2ms TTL=61
        Reply from 10.1.7.146: bytes=1400 time=1ms TTL=61
        </code></pre>

        > **Note:** Depending on your Operating System, the exact command for large ping may vary (e.g., `-l`, `-s`).  

        Consult with your Network Team to determine why routing does not reach the destination.  The issue is typically related to routing misconfigurations or firewall blockages.

