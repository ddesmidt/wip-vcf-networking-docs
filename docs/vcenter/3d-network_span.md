<h1>
  <img src="../../assets/vCenter.png" style="height:30px"; vertical-align:middle;> Network Span in vCenter
</h1>

<div class="grid" markdown style="grid-template-columns: 20% 80%">

<div markdown>
This section describes the procedures for configuring Network Span using the vSphere Client.  
<br><br>
**Network Span**  defines how VPC subnets span across vCenter clusters.
</div>


<div markdown>
![vCenter Network Span](images/3d-0-Network_Span.jpg){ width="100%" }
</div>

</div>

---

## Network Span

### Configuration

<div style="margin-left: 20px; margin-right: 20px;" markdown="1">
??? info ":material-information-outline: Deep Dive: Blogs & Video Demonstrations"
    * **Video Walkthrough:** Watch the step-by-step Network Span configuration [<span style="color: #FF0000;">:fontawesome-brands-youtube:</span> on YouTube](https://youtu.be/uEe5-lLIVdg){ target="_blank" }.
    * **Technical Blog:** Read the detailed Network Span blog on the [<span style="color: #007cbb;">:material-newspaper-variant-outline:</span> VMware Cloud Foundation Blog](https://blogs.vmware.com/cloud-foundation/2026/06/09/vcf-9-1-networking-precision-workload-placement-with-vpc-network-span/){ target="_blank" }.
</div>

#### Step1. Create Network Span
![vCenter Network Span config](images/3d-1a-Create_Network_Span.jpg){ width="95%" style="display: block; margin: 0 auto;" }


### Monitoring

#### Status
The status reflects the successful application of the configuration.

<div style="margin-left: 40px; margin-right: 40px;" markdown="1">
??? info "Note about the Status"
    Because this represents a logical configuration mapping rather than an active link-state protocol, the status will typically remain Green (Healthy) once the settings are validated by the NSX Manager.
</div>

![vCenter Network Span validation](images/3d-1b-Validation_Network_Span.jpg){ width="95%" style="display: block; margin: 0 auto;" }


---
