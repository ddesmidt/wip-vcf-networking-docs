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
    * [VM access down](2j2-troubleshooting-vm.md)  
    * [**Pod access down**](#troubleshooting)  

</div>

<div markdown>
![VDS Architecture](images/2g1-0-k8s.jpg){ width="100%" }
</div>
</div>

---

## Troubleshooting - Pod access down {: #troubleshooting }

As described in the [Packet Walk - E/W Pod to Pod](2i3-packetwalk-pod_pod.md) section, Pod accessing a Pod traverse the following path:

#### Logical View
![Logical](images/2i3-0-LogicalView.jpg){ width="75%" style="display: block; margin: 0 auto;" }

#### Physical View
![Physical](images/2i3-0-PhysicalView.jpg){ width="95%" style="display: block; margin: 0 auto;" }



### Step1: Traffic leaves the Source Pod
This one is internal to the K8s Node and should not be blocked.

### Step2: Cross-Node Encapsulation
* **Validate the ESXi host tunnels are UP**

??? info "Status Validation"
    Navigate to **vCenter** > **Host and Clusters** > **[your vCenter Cluster]** > **Configure** > **Networking** > **Network Configuration**.  
    Ensure "Cluster Status" and "Host Status" are "Green", and ESX have at least 1 TEP IP Address:  
    > **Note:** If no workloads have been deployed on logical networks yet, it is normal to have zero tunnels established on the ESXi hosts.  

    ![NSX Host Preparation Status](images/2a-3a-NSX-Prep.jpg){ width="95%" style="display: block; margin: 0 auto;" }

* **Validate the ESXi host tunnels accept large packets (MTU)**   

??? info "Status Validation"
    ```text
    vmkping ++netstack=vxlan <remote-TEP-IP> -d -s 8900
    ```

    ??? abstract "Output example"
        <pre><code>[root@esxi-01a:~] <b>vmkping ++netstack=vxlan 10.1.3.207 -d -s 8900</b>
            PING 10.1.3.207 (10.1.3.207): 8900 data bytes
            8908 bytes from 10.1.3.207: icmp_seq=0 ttl=64 time=1.234 ms
            8908 bytes from 10.1.3.207: icmp_seq=1 ttl=64 time=1.102 ms
            8908 bytes from 10.1.3.207: icmp_seq=2 ttl=64 time=1.098 ms
            --- 10.1.3.207 ping statistics ---
            3 packets transmitted, 3 packets received, 0% packet loss
            round-trip min/avg/max = 1.098/1.144/1.234 ms
        </code></pre>
        Consult with your Network Team to determine why routing does not reach the destination.  
        
        * If standard "small" pings fail, the issue is typically related to routing misconfigurations or firewall blockages.  
        * If "large" pings fail, the issue is typically related to Jumbo Frames (MTU) not being enabled across the physical fabric.



