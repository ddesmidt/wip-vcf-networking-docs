# Draft answers (v2) for Gartner MQ Server Virtualization Platforms questionnaire

---

## Product or Service

### Q1. Platform name and latest version
VMware Cloud Foundation 9.0 (VCF). VCF is a unified private cloud platform with a comprehensive software stack covering compute, storage, network, container, and cloud management services. The platform integrates VMware vSphere, VMware vSAN, VMware NSX, VCF Operations, VCF Automation, VMware vDefend, and VMware Live Recovery (VLR) under a single licensed and lifecycle-managed instance, with the management domain and one or more workload domains as the standard topology.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.6874 (Administration SDKs, APIs, and CLI); vmware-cloud-foundation-9-0-complete.pdf p.1422 (Managing VCF Domains)

### Q2. Container deployment and management
Yes, through containers inside VMs and through another technique. VMware Kubernetes Service (VKS) provisions upstream Kubernetes clusters as VMs in a vSphere Namespace and is delivered as a Supervisor Service decoupled from Supervisor releases. vSphere Supervisor embeds a Kubernetes control plane directly into ESX hosts, exposing a unified declarative API surface, and vSphere Pods run containers directly on ESX hosts without needing a separate Kubernetes cluster.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.402 (What Is VMware Cloud Foundation > vSphere Supervisor); vsphere-supervisor-services-and-standalone-components.pdf p.167 (Installing and Upgrading VKS)

### Q3. Native Kubernetes integration
- **Native: Yes.** VKS is the native, conformant Kubernetes distribution included with VCF. VKS is installed as a Supervisor Service in vSphere 9, supports current upstream Kubernetes releases (for example v1.34.1 with VKR extensions and v1.31.4 with FIPS), and is provisioned through the Cluster v1beta1/v1beta2 API. Supervisor provides default networking for nodes, pods, and services, and translates VirtualMachineService resources into Kubernetes service objects.
- **Third-party: Yes.** VCF runs Red Hat OpenShift, SUSE Rancher, Google Anthos, and Amazon EKS-A as ordinary VM workloads. These distributions are not natively integrated with the Supervisor control plane.

**Supporting facts:** vsphere-supervisor-services-and-standalone-components.pdf p.167 (Installing and Upgrading VKS); vsphere-supervisor-services-and-standalone-components.pdf p.266 (Provisioning VKS Clusters); vsphere-supervisor-services-and-standalone-components.pdf p.289 (v1beta1/v1beta2 Default Cluster); third-party distribution support is external to ChromaDB.

---

## Compute - CPU

### Q4. Minimum number of physical nodes
A VCF deployment is organized into a management domain and one or more workload domains. Each cluster in a workload domain must start with a minimum of three ESX hosts and can scale up to 64 hosts; the same minimum applies to the management domain default cluster. ESX hosts must be commissioned before they can be added to an SDDC cluster, and commissioned hosts are scoped to either vSAN OSA or vSAN ESA at commissioning time and cannot be mixed within a cluster.

For stretched topologies, a vSAN stretched cluster requires at least three ESX hosts in each availability zone plus a witness host deployed in a separate witness zone outside the availability zones. NSX federation and stretched networking complement vSAN stretched clusters at the network layer. Within VCF's reference architecture options, a compute cluster (a smaller cluster type used in specific rack designs) can contain two or three hosts based on the workload needs and vCenter requirements; this is distinct from the standard workload-domain cluster minimum of three.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.2972 (VMware Cloud Foundation Account in VCF Operations: workload domain cluster minimum 3, maximum 64); vmware-cloud-foundation-9-0-complete.pdf p.1422 (Managing VCF Domains: management and workload domains); vmware-cloud-foundation-9-0-complete.pdf p.1266 (Supported Configurations: vSAN Stretched Clusters require at least 3 ESX hosts in each availability zone and a witness host in a separate witness zone); vmware-cloud-foundation-9-0-complete.pdf p.1583 (Compute Cluster: two or three hosts based on workload needs and vCenter requirements); vmware-cloud-foundation-9-0-complete.pdf p.1419 (Commission ESX Hosts: OSA/ESA scope at commissioning)

### Q5. Processor architecture support
x86-64 is the primary supported architecture, including Intel Xeon (through current Granite Rapids generations) and AMD EPYC families (Rome and later). Enhanced vMotion Compatibility (EVC) is used to align processor feature sets across mixed generations within a single CPU vendor for live migration; AMD and Intel cannot be mixed within an EVC cluster, and AMD-V or Intel VT, plus AMD NX or Intel XD, must be enabled in BIOS. ARM support is limited and intended for edge use cases (for example NVIDIA Grace and Ampere-class systems running ESX-on-ARM); RISC-V and IBM POWER are not supported.

**Supporting facts:** vmware-vsphere-9-0.pdf p.946 (CPU Compatibility and EVC Pre-defined Modes); vmware-vsphere-9-0.pdf p.2044 (CPU Virtualization > EVC); vmware-cloud-foundation-9-0-complete.pdf p.1967 (NSX Edge Bare Metal AMD EPYC support); ARM/RISC-V/POWER scope is external to ChromaDB.

### Q6. Maximum VM size
A virtual machine on hardware version 22 supports up to 960 virtual CPUs and 24,560 GB (approximately 24 TB) of RAM. Virtual disks reach up to 62 TB each on hardware version 13 or later. Each VMware Paravirtual SCSI controller supports up to 64 disks; LSI Logic SAS and BusLogic controllers support up to 15 disks each. A VM supports up to 10 vNICs on hardware version 7 or later, and up to 16 PCI passthrough devices on hardware versions 13 through 19. Refer to configmax.broadcom.com for the consolidated limits across controllers.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1042 (Virtual CPU Configuration and Limitations); vmware-vsphere-9-0.pdf p.1032 (Hardware Features Available with VM Compatibility Settings, max memory 24560 GB, max disk 62 TB, max NICs 10, max PCI passthrough 16); vmware-vsphere-9-0.pdf p.2473 (Allocate Memory Resources)

### Q7. CPU usage features
Preemptive scheduling with oversubscription, oversubscription monitoring, vCPU pinning, CPU reservations, vNUMA, and vCPU hot-add are all supported. CPU resource allocation uses shares, reservations, and limits to prioritize VMs when host capacity does not meet demand. Virtual NUMA topology is enabled by default for wide VMs and can be tuned through advanced configuration. CPU affinity assigns a VM to specific physical processors, with the caveat that admission control does not consider affinity. Hot-add is supported up to 128 vCPUs while the VM is running; expansions beyond 128 require a power cycle.

**Supporting facts:** vmware-vsphere-9-0.pdf p.2469 (Virtual CPU hot-add limit); vmware-vsphere-9-0.pdf p.2160 (Using Virtual NUMA); vmware-vsphere-9-0.pdf p.2048 (CPU affinity); vmware-vsphere-9-0.pdf p.2469 (CPU shares, reservations, limits); vmware-vsphere-9-0.pdf p.2044 (CPU Virtualization)

### Q8. Memory features
Supported: oversubscription with VMkernel-driven reclamation, memory ballooning, swapping, memory tiering over NVMe (new in vSphere 9.0), memory utilization monitoring, active memory reporting through performance counters, and memory hot-add. Transparent page sharing across VMs is off by default for security; salted intra-VM page sharing is retained. Memory Tiering over NVMe is configured per ESX host or per cluster and requires NVMe devices on the supported device list (also applicable on vSAN). The VMkernel reclaims unused VM memory through ballooning first and falls back to swapping if needed; the Memory Consumed counter is recommended for capacity analysis.

**Supporting facts:** vmware-vsphere-9-0.pdf p.2069-2070 (Memory Tiering over NVMe Configuration); vmware-vsphere-9-0.pdf p.2182 (Memory Tiering on vSAN); vmware-vsphere-9-0.pdf p.2346 (Memory ballooning and swapping); vmware-vsphere-9-0.pdf p.1032 (Memory ballooning hardware version 4+); vmware-cloud-foundation-9-0-complete.pdf p.3987 (Memory Consumed counter)

### Q9. Certified hardware lists, reference architectures, and hardware requirements
Certified hardware for VCF is published in the Broadcom Compatibility Guide (servers, I/O adapters, GPUs, storage) and the vSAN ReadyNode catalog of validated server configurations. VCF Validated Designs and the VCF Design Library publish tested reference architectures and architectural options. Configuration maxima for cluster, host, VM, and platform limits are published separately. Tier-1 hardware partners include Dell, HPE, Lenovo, Cisco, Hitachi Vantara, Fujitsu, and Supermicro.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.412 (Design library for architectural options); compatibility lists and partner ecosystem are external to ChromaDB.

### Q10. Public cloud support
VCF software is delivered or available as a managed offering across major hyperscalers, including Amazon Web Services, Microsoft Azure, Google Cloud, Oracle Cloud Infrastructure, IBM Cloud, and Alibaba Cloud. Availability on Huawei Cloud and Tencent Cloud is limited or regional. Specific service names, SKUs, and supported regions vary by provider; refer to the provider's VMware-cloud landing page for current scope.

**Supporting facts:** External; not in ChromaDB.

---

## Compute - GPU

### Q11. GPU sharing mechanism
VCF supports hardware-assisted GPU sharing through NVIDIA vGPU and NVIDIA Multi-Instance GPU (MIG), and full-device assignment through DirectPath I/O passthrough and Dynamic DirectPath I/O. NVIDIA MIG securely partitions an applicable GPU into separate GPU instances, and unique vGPU profile names can be assigned per VM when the GPU is in MIG mode. Multiple NVIDIA GRID vGPUs assigned to a single VM require profiles with the maximum frame buffer. These mechanisms underpin VMware Private AI Foundation with NVIDIA, which integrates the NVIDIA GPU Operator and NGC container catalog and supports VM classes with vGPU or GPU passthrough for AI workloads.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1079 (NVIDIA MIG GPU partitioning, vGPU profiles in MIG mode); vmware-vsphere-9-0.pdf p.1079 (Multiple GRID vGPUs require max frame buffer profiles); vmware-vsphere-9-0.pdf p.1338 (DirectPath I/O passthrough); vmware-vsphere-9-0.pdf p.1032 (Dynamic DirectPath available in HW v17+); vmware-private-ai-foundation-with-nvidia-9-0.pdf p.46-49 (PAIF GPU Operator, supported NVIDIA GPU devices); vmware-private-ai-foundation-with-nvidia-9-0.pdf p.50 (MIG sharing per ESX host)

### Q12. GPU vendors and models supported
Primary support is for NVIDIA data center GPUs running NVIDIA AI Enterprise vGPU profiles, including A100, A30, A40, L40, L40S, H100, H200, and B200-class accelerators. AMD Instinct MI-series and Intel Data Center GPU Flex and Max series are supported through DirectPath I/O passthrough. The authoritative list of certified GPUs is the VMware Compatibility Guide; for Private AI Foundation, the supported GPU list is published in the PAIF documentation.

**Supporting facts:** vmware-private-ai-foundation-with-nvidia-9-0.pdf p.49 (Supported NVIDIA GPU Devices, vGPU profile requirement); vmware-vsphere-9-0.pdf p.1079 (NVIDIA MIG and vGPU profile assignment); specific AMD Instinct and Intel Data Center GPU model lists are external to ChromaDB.

### Q13. Live migration of VMs attached to GPUs or vGPUs
vSphere vMotion supports live migration of NVIDIA vGPU-powered VMs without data loss when the `vgpu.hotmigrate.enabled` advanced setting is configured. During the migration the VM is briefly inaccessible during stun time, then resumes with applications continuing from their previous state. Storage vMotion of vGPU VMs also requires `vgpu.hotmigrate.enabled` set to true. The source and destination hosts must expose matching vGPU profiles and compatible NVIDIA host driver versions, and Dynamic DirectPath I/O is not supported on VMs with hot-plug of passthrough devices enabled. VMs assigned full GPUs through DirectPath I/O passthrough cannot be live-migrated; they require shutdown.

**Supporting facts:** vmware-vsphere-9-0.pdf p.913 (vMotion with vGPU stun time and live migration with vgpu.hotmigrate.enabled); vmware-vsphere-9-0.pdf p.921 (vgpu.hotmigrate.enabled for Storage vMotion); vmware-vsphere-9-0.pdf p.1340 (Dynamic DirectPath I/O not supported with passthrough hot-plug)

---

## Networking

### Q14. Network features
- **NetFlow:** Yes. The vSphere Distributed Switch (vDS) supports NetFlow with IPFIX (NetFlow v10) export, configurable per distributed port group, with a switch-level Switch IP address option to consolidate flows from multiple hosts in the collector. NSX adds collector reachability over IPv4 or IPv6.
- **sFlow:** No on vDS. Flow telemetry on VCF is delivered via IPFIX (NetFlow v10) on the vDS and via IPFIX-based flow data in NSX, not sFlow.
- **LACP:** Yes. The vDS supports Link Aggregation Groups (LAGs) with LACP in Active or Passive negotiating mode and multiple LAGs per switch to aggregate physical NIC bandwidth.
- **NIC teaming beyond LACP:** Yes. Distributed port groups support teaming and failover policies including route based on originating port ID, source MAC hash, IP hash, physical NIC load (load-based teaming), and explicit failover, with port-level overrides.
- **Private VLANs:** Yes. The vDS supports PVLANs with a Promiscuous primary VLAN and Isolated or Community secondary VLANs; primary and secondary IDs must match the upstream PVLAN-aware switch configuration.
- **IPv6:** Yes. ESX, VMkernel services, and guest workloads support IPv6, including dual-stack and IPv6-only management; NSX and Avi Load Balancer also support dual-stack data paths.
- **Port Mirroring:** Yes. The vDS and NSX support local SPAN, Remote SPAN (RSPAN), and Encapsulated Remote SPAN (ERSPAN) with GRE, ERSPAN Type II, and ERSPAN Type III encapsulation, configured via the mirror TCP/IP stack.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1405 (Monitor Network Connection and Traffic > NetFlow Settings); vmware-vsphere-9-0.pdf p.1337 (How to Use VLANs to Isolate Network Traffic > Private VLANs); vmware-cloud-foundation-9-0-complete.pdf p.2418 (Advanced Network Management with NSX > Network Monitoring > Add a Port Mirroring Session)

### Q15. Virtual network devices
VCF provides multiple virtual network device types. The vSphere Standard Switch (vSS) is host-local, while the vSphere Distributed Switch (vDS) spans multiple ESX hosts and is the recommended switch for VCF deployments. NSX adds overlay segments and Edge Bridges, which extend an overlay segment to a traditional VLAN at Layer 2 by instantiating active or active/standby bridges on NSX Edge nodes. Routing and address translation are provided by NSX Tier-0 and Tier-1 gateways: NAT (SNAT/DNAT) is configured on the uplinks of Tier-0 or Tier-1 gateways and processes traffic traversing those interfaces.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.1925 (Advanced Network Management with NSX > Transport Zones and Profiles > NSX Edge Bridge Profile); vmware-cloud-foundation-9-0-complete.pdf p.2071 (Advanced Network Management with NSX > Network Address Translation); vmware-vsphere-9-0.pdf p.1569 (Designing vSAN Network > Migrating from Standard to Distributed vSwitch)

### Q16. Bandwidth allocation and prioritization
VCF provides bandwidth controls at the vSphere and NSX layers. vSphere Network I/O Control (NIOC) divides vDS traffic into predefined resource pools (Fault Tolerance, iSCSI, vMotion, management, vSphere Replication, NFS, virtual machine), with shares, reservations, and limits per pool. NIOC reservations can guarantee minimum bandwidth to system traffic types and can be applied at the vNIC level for individual virtual machines. NIOC enforces a ceiling of 75 percent of a physical adapter for reservations (for example, 7.5 Gbps on a 10 GbE NIC). For vSAN, shares are the recommended allocation method, and limits are discouraged because traffic types with limits cannot consume additional bandwidth even when available. NSX adds QoS Segment Profiles that use Class of Service (CoS) to group similar traffic types and assign priority, slowing or dropping lower-priority traffic to give better throughput to higher-priority flows. NSX Tier-1 gateways support gateway QoS profiles that define traffic-rate limits via permitted information rate and burst size, applied to ingress and egress for unicast, BUM, and IPv4 traffic.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.1521 (Designing vSAN Network > NIOC); vmware-vsphere-9-0.pdf p.1370 (vSphere Network I/O Control > Allocate Bandwidth for System Traffic); vmware-cloud-foundation-9-0-complete.pdf p.1826 (Advanced Network Management with NSX > Segments > QoS Segment Profile); vmware-cloud-foundation-9-0-complete.pdf p.2134 (Networking Settings > Configure Global Gateway Settings)

### Q17. Networking features
- **Firewall:** Supported. vDefend Distributed Firewall is a software-defined Layer 2-7 firewall enforced at each workload to segment east-west traffic, with policies organized into Ethernet, Emergency, Infrastructure, Environment, and Application categories, plus tagging of VMs, segments, and segment ports for policy grouping. vDefend Gateway Firewall is enforced at NSX Tier-0 and Tier-1 gateways for north-south traffic.
- **Load Balancer:** Supported. VMware Avi Load Balancer is the strategic Layer 4-7 load balancer, with virtual services, GSLB across multiple sites, IPv4/IPv6 dual-stack VIPs, and an integrated WAF. NSX also provides a built-in load balancer with Layer 4 virtual servers attached to server pools and a Distributed Load Balancer mode.
- **IPAM:** Supported. NSX manages overlay IP allocation natively. Avi includes built-in IPAM and DNS, with optional integration with Infoblox for IPAM and DNS, and partner integrations for BlueCat.
- **Microsegmentation:** Supported. vDefend Distributed Firewall provides microsegmentation between applications, application tiers, and microservices, with Security Intelligence Segmentation Planning to design and optimize policies.
- **BGP:** Supported. NSX Tier-0 gateways support BGP with BFD for fast neighbor failure detection, ECMP for multi-path forwarding, and route maps. EVPN over BGP is supported for tenant overlay use cases.

**Supporting facts:** vmware-vdefend-firewall-9-0.pdf p.22 (vDefend Firewall Overview); vmware-cloud-foundation-9-0-complete.pdf p.2083 (NSX Load Balancer > Key Concepts); vmware-avi-load-balancer-31-2.pdf p.555 (Avi Load Balancer IPAM and DNS); vmware-cloud-foundation-9-0-complete.pdf p.1777 (Tier-0 Gateways > Configure BGP)

### Q18. Network adapter categories
ESX includes native drivers for 25 GbE, 40 GbE, 100 GbE, and 200 GbE Ethernet adapters from major vendors, with 400 GbE coverage on certified parts. The Broadcom Compatibility Guide is the authoritative list for supported speeds, vendors, and firmware. InfiniBand is supported through partner-supplied drivers (NVIDIA Mellanox ConnectX family) where the adapter is on the Compatibility Guide; HDR (200 Gbps) and NDR (400 Gbps) are available on certified ConnectX-6 and ConnectX-7 cards, typically deployed in passthrough or SR-IOV mode for AI and HPC workloads. Customers should confirm the specific adapter model, firmware, and ESX version against the Compatibility Guide before procurement.

**Supporting facts:** External; not in ChromaDB. Specific NIC model coverage is published on the Broadcom Compatibility Guide rather than in product documentation.

### Q19. Advanced networking for AI
VCF supports the network fabrics and offload technologies that AI and HPC workloads require. RDMA over Converged Ethernet (RoCE v2) is supported on ESX for storage and east-west fabric use, including NVMe over RDMA, with the appropriate RoCE-capable adapter and a lossless Ethernet network. InfiniBand HDR and NDR are supported through certified partner adapters (NVIDIA ConnectX-6/7) for AI and HPC deployments where ultra-low latency is required.

VCF supports vSphere Distributed Services Engine, which offloads network operations from the host CPU to a Data Processing Unit (DPU, also called SmartNIC). NVIDIA BlueField and AMD Pensando DPUs are supported, with vDS Network Offloads Compatibility configured at the switch level to direct networking to the DPU. NSX traffic monitoring (including ERSPAN) takes advantage of DPU offload, with full or partial offload depending on the DPU vendor. Multiple-NIC vMotion can be configured by adding two or more NICs to a standard or distributed switch to allocate higher migration bandwidth, which benefits movement of large GPU-attached VMs. NIOC isolates AI fabric traffic from management and storage traffic on shared uplinks. NSX provides overlay segmentation between tenant AI workloads, and vDefend Distributed Firewall provides east-west microsegmentation for those workloads.

VMware Private AI Foundation with NVIDIA (PAIF) builds on this infrastructure, integrating the NVIDIA GPU Operator for GPU resource management and NVIDIA NGC container images on VKS clusters, with VM classes configured for GPU passthrough.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1355 (Manage Network Resources > RDMA over Converged Ethernet); vmware-cloud-foundation-9-0-complete.pdf p.2421 (NSX > vSphere Distributed Services Engine and DPU support); vmware-vsphere-9-0.pdf p.911 (Migrating Virtual Machines with vSphere vMotion > Multiple-NIC vMotion); vmware-private-ai-foundation-with-nvidia-9-0.pdf p.49 (Supported NVIDIA GPU Devices)

---

## Storage

### Q20. Storage networking protocols
VCF 9.0 supports Fibre Channel, FCoE, iSCSI (software initiator and dependent or independent hardware initiators), and NVMe over Fabrics. NVMe-oF transports include NVMe over Fibre Channel, NVMe over RDMA with RoCEv2, and NVMe over TCP. NVMe over Fibre Channel reuses existing FC infrastructure upgraded for NVMe; NVMe over TCP uses Ethernet with VMkernel binding on a Software NVMe over TCP adapter; NVMe over RDMA requires RoCEv2-capable adapters. ESX hosts use FC HBAs for FC and NVMe-FC, Ethernet adapters for software iSCSI and NVMe-TCP, and dependent or independent hardware iSCSI HBAs where vendor offload is required. FCoE is supported but is positioned as legacy and is generally not the recommended choice for new deployments. Storage protocol selection feeds into datastore types (VMFS, NFS, vVols) and into NVMe namespaces presented to the host.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1448 (Networked Storage > NVMe over Fabrics Storage); vmware-vsphere-9-0.pdf p.1533 (About VMware NVMe Storage > Basic VMware NVMe Architecture and Components); vmware-vsphere-9-0.pdf p.1538 (Requirements for NVMe over TCP); vmware-vsphere-9-0.pdf p.1510 (Managing iSCSI Session on ESX Host)

### Q21. External storage array compatibility list
The authoritative HCL is the Broadcom Compatibility Guide, which publishes separate searches for SAN arrays and host adapters and for vVols-capable arrays and VASA providers. The vSphere Storage Guide enumerates supported transports (FC, FCoE, iSCSI, NFS v3 and v4.1, NVMe over FC, NVMe over TCP, NVMe over RDMA) for vVols.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1667 (Virtual Volumes and VM Storage Policies, vVols transport list); vmware-vsphere-9-0.pdf p.1668 (Virtual Volumes and Storage Protocols)

### Q22. Storage device types
VCF 9.0 supports all listed storage device types. External arrays are consumed through FC, FCoE, iSCSI, NVMe-oF, and NFS, and through vSphere Virtual Volumes (vVols) for policy-driven array integration. Hyperconverged storage on the same nodes is delivered by vSAN, with two architectures: vSAN Original Storage Architecture (OSA) using cache plus capacity disk groups, and vSAN Express Storage Architecture (ESA), where each contributing host presents a single storage pool of flash devices. Storage-only or disaggregated configurations are delivered by a vSAN storage cluster (vSAN Max), an ESA-only deployment that pools storage devices across dedicated ESX hosts and serves vSAN and vSphere clusters that consume it remotely. vSAN storage clusters are configured at cluster creation. Heterogeneous host configurations are technically supported, but uniform host configuration is the recommended practice for predictable vSAN performance. SPBM provides a single policy abstraction over external arrays, vSAN, and vSAN Max.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.439 (Storage Models > Principal and Supplemental Storage); vmware-cloud-foundation-9-0-complete.pdf p.1572 (Planning and Configuring vSAN); vmware-cloud-foundation-9-0-complete.pdf p.1686 (Claim Storage Devices for vSAN ESA Cluster)

### Q23. Storage live migration
Storage vMotion migrates a running VM and its disk files from one datastore to another with no downtime. Migration is supported across VMFS, vSAN, and Virtual Volumes datastores, and vCenter performs compatibility checks before the operation. The source and destination can be different storage types, so a VM on FC VMFS can be moved to NFS, vSAN, or vVols. Disk format can be changed during the move (thin to thick, or thick to thin), and the disks remain consistent with active snapshots. Storage vMotion is also used to evacuate a datastore placed in maintenance mode and is the migration engine that Storage DRS uses for automated load balancing across a datastore cluster. Advanced Cross vCenter vMotion combines compute and storage relocation in a single operation, including across vCenter instances and between on-premises and cloud environments, and supports import or clone of VMs into the target inventory.

**Supporting facts:** vmware-vsphere-9-0.pdf p.920 (What is Migration with Storage vMotion); vmware-vsphere-9-0.pdf p.1564 (VMFS Datastores as Repositories, cross-datastore svMotion); vmware-vsphere-9-0.pdf p.927 (Advanced Cross vCenter vMotion)

### Q24. Storage features
VCF 9.0 supports all listed storage features. VMs use VMDK virtual disks on VMFS, NFS, vVols, vSAN, and vSAN Max. Storage QoS at the virtual hard disk level is configured per VMDK as a Limit IOPs setting (with an Unlimited option) and through Storage I/O Control shares. Thin provisioning is a per-disk format that allocates capacity on demand. Automatic space reclamation uses the SCSI UNMAP primitive: VMFS6 issues UNMAP automatically when guest filesystems free blocks, VMFS5 supports UNMAP for a limited set of guest operating systems, and the NAS UNMAP primitive supports thin-provisioned NFS 3 datastores. The released space is then reclaimed by the array. Non-persistent disks are configured as Independent Nonpersistent disk mode, which discards changes when the VM is powered off or reset, while Independent Persistent writes data permanently and Dependent disks are included in snapshots. Per-VM data services (compression, encryption, FTT, mirroring) are enabled and disabled per object via SPBM and vSAN storage policies, including vSAN ESA where compression is set per object via the policy.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1732 (Space Reclamation Requests from Guest Operating Systems, VMFS6 UNMAP); vmware-vsphere-9-0.pdf p.2480 (VMware Host Client > Limit IOPs); vmware-cloud-foundation-9-0-complete.pdf p.1630 (vSAN ESA per-object compression via storage policy)

### Q25. SAN and storage device-to-VM mapping
VCF 9.0 supports all listed mechanisms. Raw Device Mapping (RDM) presents a LUN directly to a VM through a mapping file in a VMFS volume, and is used where a VM requires raw device access (for example, shared storage for WSFC or applications that need SCSI passthrough). RDM is available in virtual and physical compatibility modes, but virtual disks remain the preferred option for general manageability. Multipathing is provided in the hypervisor by the Pluggable Storage Architecture, with the Native Multipathing Plug-in (NMP) for traditional SCSI devices and the High-Performance Plug-in (HPP) as the default for high-speed devices including NVMe and NVMe-oF. NMP uses SATPs for failover and PSPs (Most Recently Used, Fixed, Round Robin including the Latency sub-policy) for path selection; HPP uses Path Selection Schemes with Load Balance Round Robin (LB-RR) as the default. Path failover and load balancing are automatic. ESX hosts can boot from FC SAN, iSCSI SAN (with software iSCSI requiring an iBFT-capable NIC, or with an independent hardware iSCSI HBA), and from NVMe over FC namespaces using a Boot-from-SAN-enabled HBA and an SMBIOS-UUID-based Host NQN. NPIV provides per-VM Fibre Channel virtual ports that share a single physical HBA, each with unique identifiers for zoning and masking.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1633-1635 (Raw Device Mapping introduction and characteristics); vmware-vsphere-9-0.pdf p.1607-1613 (PSA, NMP, HPP, Path Selection Schemes); vmware-vsphere-9-0.pdf p.1477, p.1559 (Boot from iSCSI SAN, NVMe over FC boot); vmware-vsphere-9-0.pdf p.978 (NPIV)

### Q26. Inline deduplication and compression
vSAN ESA enables compression by default through the vSAN storage policy, applied at the object level so it can be turned on for one object and off for another in the same cluster. ESA compression operates on new writes, so existing data remains uncompressed unless rewritten. vSAN OSA offers a cluster-level setting for compression-only or for combined deduplication and compression, and these services are available only on all-flash disk groups. The compression-only option is positioned for OSA workloads that do not benefit from deduplication. External storage arrays consumed over FC, iSCSI, NVMe-oF, NFS, or vVols use their own array-side dedupe and compression engines, exposed to vSphere through SPBM where the array's VASA provider advertises capabilities. Activation, scope, and rate are array-specific and are managed at the array.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.1573 (vSAN ESA enables compression by default via storage policy); vmware-cloud-foundation-9-0-complete.pdf p.1695-1697 (Deduplication and Compression in vSAN Cluster, OSA all-flash requirement, ESA new-writes only); vmware-cloud-foundation-9-0-complete.pdf p.1573 (Compression-only for vSAN OSA)

### Q27. Automatic data tiering
VCF 9.0 introduces vSphere Memory Tiering, which extends host memory by combining DRAM with NVMe devices configured as a memory tier. The default DRAM-to-NVMe ratio is 1:1, so a host with 256 GB of DRAM and a 4 TB NVMe device exposes 256 GB of tiered NVMe memory; configuring a larger tier partition still caps usable tiered memory at the ratio. The tiering engine places hot pages in DRAM and cooler pages on NVMe transparently to the guest. For storage, vSAN ESA uses a single storage pool of flash devices per host rather than the OSA cache-plus-capacity model, so ESA does not require traditional intra-host tiering, while OSA continues to use cache and capacity tiers. External arrays perform their own array-side tiering across media classes, and SPBM is the policy plane that places VMs on the right datastore or storage class based on capability tags advertised by the array's VASA provider.

**Supporting facts:** vmware-vsphere-9-0.pdf p.2063-2064 (Memory Tiering over NVMe, Capacity Planning, DRAM:NVMe 1:1 default); vmware-vsphere-9-0.pdf p.2184 (System Memory Total Displays Less NVMe Tiered Memory, ratio limit example); vmware-cloud-foundation-9-0-complete.pdf p.1686, p.1758 (vSAN ESA single storage pool of flash devices)

---

## Security

### Q28. Security features
VCF supports all of the listed controls. Role-based access control is provided through vCenter, where administrators create custom roles from fine-grained privileges (for example, `Host > Configuration > Memory Configuration`) and apply them to users or groups against any inventory object, including global permissions. Multifactor authentication is delivered through identity provider federation by way of VCF Identity Broker and VCF Single Sign-On (VCF SSO), which integrate with external IdPs over OIDC and SAML 2.0 (Microsoft Entra ID, Microsoft AD FS, Ping Identity, Okta and others). The IdP enforces the authentication factors (push, OTP, smart card, FIDO2). VCF supports two or more directory sources concurrently: Active Directory over LDAPS and OpenLDAP for legacy identity sources, and federated OIDC or SAML for modern identity. Administrative actions across vCenter, ESX, NSX, vSAN and the VCF management plane are emitted to remote syslog targets and can be analyzed in VCF Operations for Logs, which ingests data from any source via syslog. Cryptographic modules in VCF (NSX, Avi Load Balancer, VCF Automation) are validated to FIPS 140-3 by the NIST Cryptographic Module Validation Program; VCF Operations for Networks runs in FIPS-validated mode. Customer SSL/TLS certificates and PKI are supported: the Certificate Manager and the Certificate Management UI in vCenter replace machine SSL certificates with VMCA-issued or external CA certificates.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1779, 1786 (vSphere Permissions and User Management Tasks); vmware-cloud-foundation-9-0-complete.pdf p.2778, 2788, 2808 (Configuring VCF Single Sign-On); vmware-cloud-foundation-9-0-complete.pdf p.2620 (NSX FIPS 140-3 cryptographic modules); vmware-cloud-foundation-9-0-complete.pdf p.5822 (VCF Automation FIPS 140-3); vmware-vsphere-9-0.pdf p.490, 495 (vSphere Security Certificates)

### Q29. Encryption and attestation features
VCF supports TPM 2.0, vTPM, guest VM encryption and post-quantum cryptography readiness. ESX hosts use a TPM 2.0 chip to attest to host identity by authenticating the state of the host software at a given point; ESX must have UEFI Secure Boot enabled to pass TPM attestation, and the TPM hardware vendor is verified during attestation key creation. A virtual TPM (vTPM) is a software representation of a TPM 2.0 chip that can be added to new or existing virtual machines, supporting Windows 11, BitLocker and Virtualization-Based Security workloads. VM encryption protects guest VMs and virtual disks; keys can be supplied by vSphere Native Key Provider, which enables vTPMs, vSphere Virtual Machine Encryption and vSAN Data at Rest Encryption without an external key server, or by an external KMIP 1.1 key management server (Thales, Entrust, Fortanix, HashiCorp, IBM and other validated providers). Encrypted vMotion is supported in deactivated, opportunistic and required modes and is required for encrypted VMs. vSAN provides data-at-rest encryption of the entire vSAN datastore and cluster-wide data-in-transit encryption of all data and metadata between hosts. Post-quantum readiness is delivered as cryptographic modules across VCF align with the published FIPS 140-3 and NIST guidance; specific NIST PQC algorithm support is delivered with the underlying validated modules.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1847 (Securing ESX Hosts with TPM); vmware-vsphere-9-0.pdf p.1107, 1108, 1940 (Securing Virtual Machines with vTPM); vmware-vsphere-9-0.pdf p.1917 (Configuring and Managing vSphere Native Key Provider); vmware-vsphere-9-0.pdf p.1910, 7470 (Configuring and Managing a Standard Key Provider, KMIP 1.1); vmware-vsphere-9-0.pdf p.978, 1930 (Encrypted vMotion); vmware-cloud-foundation-9-0-complete.pdf p.1700, 1701 (vSAN Data-In-Transit Encryption); vmware-vsphere-9-0.pdf p.2560 (vSAN data at rest encryption). PQC algorithm specifics not in ChromaDB; verify against VCF 9.0 release notes before submission.

### Q30. Security best practices, hardening and KB references
Hardening guidance for VCF 9.0 is published by Broadcom and the U.S. Department of Defense. The VMware Cloud Foundation Security Configuration Guide (SCG) is the primary control reference. The DISA STIG for VMware vSphere covers ESX, vCenter, and the underlying components. VMware Security Advisories (VMSA) publish vulnerability and patch information. The VCF product documentation publishes operational security guidance, the certificate management workflows for vCenter, the FIPS configuration guidance for NSX and VCF Operations, and the SSO and Identity Broker setup guides. The hardening guidance VCF officially supports is the SCG and the DISA STIG; CIS benchmark content is provided by third-party compliance tooling rather than published by Broadcom.

**Supporting facts:** External documentation links; not in ChromaDB. (FIPS configuration referenced by vmware-cloud-foundation-9-0-complete.pdf p.2620 (NSX), p.5822 (VCF Automation), p.3454 (VCF Operations for Networks).)

---

## Management

### Q31. Native automated dynamic VM load balancing
vSphere Distributed Resource Scheduler (DRS) provides native dynamic load balancing across hosts in a cluster by redistributing virtual machines using vMotion based on cluster compute and memory utilization. DRS operates in fully automated, partially automated or manual modes, and per-VM custom automation overrides are supported. Affinity and anti-affinity rules keep VMs together on the same host, separate VMs across hosts (for example clustered application instances), or pin VMs to host groups. Resource pools allow administrators to allocate compute capacity using shares, reservations and limits, with expandable reservations supported for hierarchical pools. Predictive DRS extends DRS by acting on forecasted metrics provided by the VCF Operations server in addition to real-time metrics, allowing the cluster to prepare for predicted load changes. Storage DRS performs the same balancing role at the datastore-cluster level, with anti-affinity rules for virtual disks. Distributed Power Management (DPM) consolidates VMs onto fewer hosts during low-utilization periods and uses IPMI, iLO or Wake-On-LAN to power hosts on and off; DPM's Forecasted Metrics option lets it issue power proposals against a rolling forecast window.

**Supporting facts:** vmware-vsphere-9-0.pdf p.2103 (Managing Resource Pools, expandable reservations); vmware-vsphere-9-0.pdf p.2120 (Predictive DRS); vmware-vsphere-9-0.pdf p.2132, 2133 (DPM, IPMI/iLO/WOL); vmware-vsphere-9-0.pdf p.2153, 2154 (Storage DRS Anti-Affinity Rules); vmware-vsphere-9-0.pdf p.2602 (anti-affinity rules for clustered VMs); vmware-cloud-foundation-9-0-complete.pdf p.7495 (DRS dynamic redistribution)

### Q32. Management interfaces
VCF supports all of the listed interface types. Per-host CLI access is available through the ESX shell and ESXCLI, and through the VMware Host Client browser interface for individual ESX hosts. Centralized CLI is delivered through PowerCLI for vSphere, NSX, vSAN, VCF Automation and VCF Operations, covering single-cluster and multi-cluster scope across one or many vCenter instances. Centralized graphical management is delivered through the vSphere Client (vCenter), VCF Operations and VCF Automation, which manage one or many clusters and one or many vCenter instances in linked or federated mode. Per-host APIs are exposed by ESX via the vSphere Web Services API and the host management endpoints. Centralized APIs are exposed via the vSphere Automation REST API, the vSphere Web Services API, the vSAN Management APIs, the NSX Manager API, the VCF SDDC Manager API and the VCF Automation API, all programmable through the vSphere Automation SDKs and PowerCLI. The browser-based VM console is provided by the vSphere Client Web Console; a richer client console is available through VMware Remote Console (VMRC) for features such as device redirection. SaaS-style centralized administration is available through Broadcom-hosted services that integrate with VCF deployments.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1166 (Web Console interaction privilege); vmware-vsphere-9-0.pdf p.2460 (VMware Host Client and VMRC); vmware-cloud-foundation-9-0-complete.pdf p.7067 (vCenter REST API for syslog forwarding configuration); vmware-cloud-foundation-9-0-complete.pdf p.7786 (VCF PowerCLI managing vSphere inventory)

### Q33. Third-party plug-in ecosystem
Independent software vendor plug-ins integrate with vCenter, VCF Operations and VCF Automation. Monitoring includes management packs from Broadcom and partners (network insights, application performance monitoring, third-party telemetry). Backup and disaster recovery integration through VADP includes Veeam, Dell Technologies (PowerProtect), Commvault, Cohesity, Rubrik, HYCU, Veritas, IBM Spectrum Protect, NAKIVO, Druva, Asigra and Bacula. Security plug-ins integrate with vendors such as Trellix, CrowdStrike, SentinelOne, Microsoft Defender for Cloud, Fortinet and Symantec, with deeper microsegmentation and IDS/IPS delivered by VMware vDefend. Networking integration covers vendor-specific Hardware Support Managers, NSX integration with third-party partners, and Avi Load Balancer integration with external IdPs and CAs. Storage partners integrate VAAI, VASA and vVols providers from Dell, HPE, Hitachi Vantara, IBM, NetApp, Pure Storage, Lenovo, Cisco and others.

**Supporting facts:** External; not in ChromaDB.

### Q34. Bare-metal node operations
VCF supports all listed bare-metal operations. Hypervisor and platform software updates are managed by vSphere Lifecycle Manager (vLCM) using cluster images that include the ESX base image, vendor add-ons, components and firmware add-ons, with VCF Operations orchestrating cross-component lifecycle for vCenter, NSX, vSAN, VCF Automation and the supporting appliances. BIOS and firmware updates are delivered by the Hardware Support Manager (HSM) plug-in for the server vendor, which retrieves available firmware versions and matched drivers and applies them through the vLCM image workflow; HSM also handles DPU firmware. Power on, power off, suspend and resume operations are issued through vCenter and the underlying host management interfaces. Distributed Power Management uses IPMI, iLO or Wake-On-LAN to bring hosts in and out of standby. The VCF management appliances (vCenter, NSX Manager, VCF Operations, VCF Automation, VCF Operations for Logs and others) are delivered as virtual appliance VMs.

**Supporting facts:** vmware-vsphere-9-0.pdf p.642, 643 (vLCM remediation, HA admission control restoration); vmware-vsphere-9-0.pdf p.682, 719, 720 (vLCM images, Hardware Support Manager firmware); vmware-vsphere-9-0.pdf p.748, 750 (vLCM staging and remediation); vmware-vsphere-9-0.pdf p.2132, 2133 (DPM IPMI/iLO/WOL)

### Q35. Platform management features
VCF supports all listed features. Logical resource pooling is delivered through resource pools and vApps with shares, reservations, limits and expandable reservations, and through DRS clusters that aggregate compute capacity across hosts. Policy-based resource management is delivered through Storage Policy Based Management (SPBM), which matches storage capabilities to VM storage policies, and through DRS policies, anti-affinity rules and VCF Automation cloud zones. Resource utilization monitoring and alerting is delivered by VCF Operations, with selective activation and deactivation of alert definitions for CPU, memory and other metrics; VCF Automation surfaces VCF Operations alert thresholds for managed resources. Telemetry and event logging are emitted by every VCF component to remote syslog targets and ingested by VCF Operations for Logs, which provides log queries, analysis, log compare and dynamic field extraction. VM cloning and templates are managed through Content Library, which stores VM templates and OVF templates and supports cloning across libraries and across vCenter instances. Affinity and anti-affinity rules are configured at the DRS cluster level for hosts and at the Storage DRS level for virtual disks. Console access is provided through the vSphere Client Web Console and VMware Remote Console without third-party plug-ins.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1641, 1644, 1646 (Storage Policy Based Management); vmware-vsphere-9-0.pdf p.1011, 1013, 1024 (Content Libraries); vmware-cloud-foundation-9-0-complete.pdf p.3107 (VCF Operations alert definitions); vmware-cloud-foundation-9-0-complete.pdf p.3241, 3251 (VCF Operations for Logs); vmware-vsphere-9-0.pdf p.2103 (resource pools and expandable reservations)

### Q36. All management features in a single console
Yes, with reasonable practical caveats. VCF Operations and VCF Automation provide a unified cross-domain management surface across compute, storage, networking, identity and lifecycle, with VCF Operations integrating with VCF Operations orchestrator and VCF Automation for catalog, IaaS and policy management. The vSphere Client (vCenter) is the day-to-day console for vSphere, vSAN and the cluster-level networking and security policy applied through NSX. Specialized capabilities have their own administrative surfaces: NSX advanced networking policy is configured in the NSX Manager UI, VMware vDefend security policy and IDS/IPS in the vDefend console, advanced Avi Load Balancer policies in the Avi Controller, and hardware operations through the vendor's Hardware Support Manager plug-in.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.5936 (Self-Service IaaS Console in VCF Automation); vmware-cloud-foundation-9-0-complete.pdf p.6485 (VCF Automation integration with VCF Operations orchestrator); vmware-cloud-foundation-9-0-complete.pdf p.6111 (VCF Automation displays VCF Operations alert thresholds)

### Q37. Migration tools from other platforms
VCF includes VMware HCX as the strategic migration tool. HCX supports vMotion, cold migration, Bulk Migration and Replication Assisted vMotion (RAV); RAV uses replication and vMotion technologies in combination to provide large-scale parallel migrations with zero downtime, and it remains resilient to latency and varied network conditions during initial sync and continuous replication. Administrators can specify a switchover window for RAV migrations. HCX OS Assisted Migration (OSAM) supports migrating 64-bit Linux guest workloads from KVM and Hyper-V hypervisors, with current support including Ubuntu 14.04 through 22.04 LTS in BIOS and EFI boot modes. WAN optimization, deduplication and compression in the HCX Interconnect appliance reduce migration data volume across the wire. For existing vSphere environments, VCF Import converts an existing vCenter and its vSphere clusters into a VCF workload domain; all clusters managed by a vCenter are imported together as a unit. Third-party migration partners (Veeam, Zerto, Commvault and others) provide complementary cross-platform migration paths through VADP.

**Supporting facts:** vmware-hcx-4-11.pdf p.135, 167, 168 (HCX migration types, RAV); vmware-hcx-4-11.pdf p.142, 143, 254 (HCX OS Assisted Migration KVM/Hyper-V, supported guest OS); vmware-cloud-foundation-9-0-complete.pdf p.1268, 1446 (VCF Import constraints)

---

## Availability and Recovery

### Q38. Availability features
VCF supports all listed availability features. vMotion provides live migration within and between clusters, with cross-vCenter vMotion and long-distance vMotion supported up to a 150 millisecond round-trip time, enabling migrations across geographically separate sites. vSphere High Availability provides dynamic failover by restarting affected VMs on surviving hosts after a host, hardware or guest failure, with admission control enabled by default. VM restart prioritization is configured through HA restart priority categories with conditions and per-priority delays before HA proceeds to the next priority, supporting orchestrated startup ordering. Fault Tolerance provides continuous availability by running a synchronized secondary VM, with test failover and test secondary restart available for validation. VM snapshots are supported for short-term operational protection, including quiesced snapshots through VMware Tools and VSS on Windows. Immutable snapshots are delivered by vSAN ESA native snapshots, where a virtual disk and its snapshots are contained in a single vSAN object, and by partner integrations including Veeam Hardened Repository and Dell PowerProtect Cyber Recovery.

**Supporting facts:** vmware-vsphere-9-0.pdf p.910, 911 (vMotion 150ms RTT, long distance, networking); vmware-vsphere-9-0.pdf p.2206 (HA restart priority condition and delay); vmware-vsphere-9-0.pdf p.666 (HA admission control default); vmware-vsphere-9-0.pdf p.2224 (Fault Tolerance test failover, test restart secondary); vmware-cloud-foundation-9-0-complete.pdf p.1576, 1589, 7639 (vSAN ESA native snapshots)

### Q39. Cluster availability features
VCF supports all listed cluster features. Stretched clustering is delivered by vSAN stretched clusters, which configure a witness host as part of the fault domain to provide active-active operation across two sites; the witness is configured by FQDN, vSAN CIDR and vSAN IP address. NSX federation and stretched networking complement vSAN stretched clusters at the network layer. Heterogeneous live migration across mixed processor generations within the same vendor is supported through Enhanced vMotion Compatibility (EVC), which configures the cluster to a baseline feature set so that vMotion succeeds across processors with different feature sets. Rolling updates are delivered by vSphere Lifecycle Manager, which evacuates a host with vMotion using DRS, applies the cluster image (ESX base, vendor add-on, components and firmware add-on) and returns the host to active service; HA admission control settings are restored after remediation completes. VCF Operations orchestrates the rolling update workflow across vCenter, NSX, vSAN and the supporting components for the entire workload domain.

**Supporting facts:** vmware-cloud-foundation-9-0-complete.pdf p.1492, 1625, 7619 (vSAN stretched cluster witness host); vmware-vsphere-9-0.pdf p.2044 (EVC across processor feature sets); vmware-vsphere-9-0.pdf p.642, 643 (vLCM cluster remediation and HA admission control restore); vmware-vsphere-9-0.pdf p.682 (vLCM image workflow)

### Q40. Integrated backup and recovery
**Yes.** VMware Live Recovery (VLR) is an integrated family that includes Site Recovery Manager (SRM) for disaster recovery orchestration, vSphere Replication for hypervisor-based VM replication, and Live Cyber Recovery (LCR) for ransomware recovery. SRM orchestrates the recovery process with replication mechanisms (vSphere Replication or array-based replication via SRM Storage Replication Adapters) to coordinate failover, test failover and reprotection. Live Cyber Recovery provides an isolated recovery environment for validating workloads before restoring them to production, with automation for network microsegmentation, VM validation, risk and mitigation analysis, remediation, data recovery and recovery iteration. vCenter and VCF Operations include image-based backup and restore for the vCenter Appliance and configuration backup for the management plane components. File-level recovery within the guest OS is provided through the VLR family workflows and through partner backup products that use VADP. Quiesced application-consistent snapshots require VMware Tools and Windows VSS for Windows guests.

**Supporting facts:** vmware-site-recovery-manager-8-8.pdf p.51, 540 (SRM orchestration with replication providers); vmware-live-cyber-recovery.pdf p.129, 336 (LCR isolated recovery environment); vmware-live-cyber-recovery.pdf p.135, 143, 161 (quiesced snapshots with VMware Tools and VSS); vmware-vsphere-9-0.pdf p.389 (vCenter image-based backup and restore)

### Q41. Third-party data protection software supported
VCF supports an extensive set of backup and recovery vendors through the vStorage APIs for Data Protection (VADP), including Veeam Backup and Replication, Dell Technologies PowerProtect Data Manager and PowerProtect DD, Commvault, Cohesity DataProtect, Rubrik Cloud Data Management, Veritas NetBackup, IBM Spectrum Protect, HYCU for VMware, Druva Data Resiliency Cloud, Asigra Tigris, NAKIVO Backup and Replication, and Bacula Enterprise. VADP supports Changed Block Tracking (CBT) for incremental backups, application-consistent quiescing through VMware Tools and Windows VSS, and snapshot-offload integrations for storage array providers. Vendor compatibility is published in the Broadcom Compatibility Guide.

**Supporting facts:** External; not in ChromaDB. (Quiesced snapshots verified in vmware-live-cyber-recovery.pdf p.135, 143.)

### Q42. Disaster recovery to public cloud platforms
VLR (Site Recovery Manager, vSphere Replication and Live Cyber Recovery) operates within VCF and supports replication between on-premises VCF deployments and Broadcom-validated VCF deployments running in public cloud regions. Customers can deploy VCF to AWS (VMware Cloud on AWS, regional availability), Microsoft Azure (Azure VMware Solution), Google Cloud (Google Cloud VMware Engine), Oracle Cloud (OCVS), IBM Cloud (IBM Cloud for VMware Solutions) and Alibaba Cloud (Alibaba Cloud VMware Service in select regions). Huawei Cloud and Tencent Cloud have limited regional VMware-validated offerings; check the latest partner availability. Replication uses vSphere Replication or array-based replication where supported, and SRM orchestrates the recovery plans.

**Supporting facts:** External; not in ChromaDB. (SRM replication and recovery plan orchestration verified in vmware-site-recovery-manager-8-8.pdf p.51, 540.)

---

## Guest OS and Application Support

### Q43. Supported guest operating systems
VCF and vSphere 9.0 support a broad set of guest operating systems documented in the Broadcom Compatibility Guide. This includes Microsoft Windows Server 2012 R2, 2016, 2019, 2022 and 2025, Windows 10 and Windows 11; Red Hat Enterprise Linux 7, 8, 9 and 10; SUSE Linux Enterprise Server 12 and 15; Ubuntu Server 18.04, 20.04, 22.04 and 24.04 LTS; Oracle Linux 7, 8 and 9; Rocky Linux 8 and 9; AlmaLinux 8 and 9; CentOS Stream; Debian; Photon OS; FreeBSD; and legacy support for IBM AIX and Oracle Solaris in approved configurations. Linux guests use VMXNET3 and PVSCSI drivers embedded in the kernel and do not require VMware Tools to load these drivers; current VMware Tools is recommended for the latest driver updates. Always validate the specific guest OS version against the Compatibility Guide for the target VCF release.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1195 (VMXNET3 and PVSCSI in Linux kernel); vmware-vsphere-9-0.pdf p.2517, 2583 (VMXNET3 adapter and Tools recommendation). Specific OS list is external (Compatibility Guide).

### Q44. Certified ISV applications
Common applications certified or validated on VCF include Microsoft SQL Server, Oracle Database, SAP HANA, SAP NetWeaver, Microsoft Exchange Server, Microsoft SharePoint, Microsoft Active Directory, IBM Db2, Splunk Enterprise, Elasticsearch, MongoDB and NVIDIA AI Enterprise (delivered as part of VMware Private AI Foundation with NVIDIA on VCF). Vendor certification matrices are published by the application vendors and validated against vSphere versions in the Broadcom Compatibility Guide.

**Supporting facts:** External; not in ChromaDB.

### Q45. VDI tools
Omnissa Horizon (divested from VMware in 2024 to KKR-owned Omnissa) is the historical VDI product that runs as a workload on VCF and integrates with NVIDIA vGPU for accelerated graphics workloads. VCF provides the underlying compute, storage, networking and lifecycle for any VDI platform deployed as a workload, including Omnissa Horizon, Citrix and others.

**Supporting facts:** External; not in ChromaDB.

### Q46. Guest operating system tools
VCF includes VMware Tools for Windows guests and Open VM Tools (open-vm-tools) for supported Linux guests. VMware Tools includes paravirtualized drivers for storage and networking; the VMXNET3 and PVSCSI drivers are embedded in the Linux kernel and do not require VMware Tools to load them, while Windows guests rely on VMware Tools for the same driver families and time synchronization, mouse and screen integration, and graceful shutdown signaling. Application-consistent quiesced snapshots require VMware Tools to be installed and running; on Windows VMs, Windows Volume Shadow Copy Service (VSS) is required and Windows Tools version 10.x or later is required for quiescing. Pre-freeze and post-thaw scripts allow application-specific quiescing on Linux guests. Tools also reports guest information back to vCenter (IP, hostname, OS family) and exposes the Guest Operations API for in-guest tasks such as customization, file transfer and program execution. Customization specifications in vCenter automate guest renaming, IP configuration, domain join and SID regeneration during cloning and deployment.

**Supporting facts:** vmware-vsphere-9-0.pdf p.1195 (VMXNET3 and PVSCSI in Linux kernel, Tools relationship); vmware-vsphere-9-0.pdf p.2583 (VMware Tools recommendation for VMXNET3 driver updates); vmware-live-cyber-recovery.pdf p.135, 143, 161 (VMware Tools and VSS for quiesced snapshots, Tools 10.x or later on Windows); vmware-vsphere-9-0.pdf p.1175 (quiesced snapshot limitations)

---

## Overall Viability

### Q47. Public/private and financials
Owner action (Yoomi/CFO office). Broadcom Inc. is publicly traded on NASDAQ (ticker AVGO). VCF is part of Broadcom's Infrastructure Software segment. Public financials are available via SEC filings.

### Q48. Customer count by industry
Owner action (Yoomi).

### Q49. Investment intentions (12 months)
Owner action (Yoomi).

### Q50. Historical and projected revenue
Owner action (Yoomi/Finance). Public financials via SEC filings.

---

## Sales Execution-Pricing

### Q51. New customers added (12 months)
Owner action (Yoomi).

### Q52. Licensing/pricing models
- On-prem perpetual: legacy support only; not actively sold
- On-prem subscription: Yes (per-core)
- Subscription-based: Yes (VCF tiered SKUs bundling vSphere, vSAN, NSX, VCF Operations, VCF Automation, VLR)
- Support plans: Yes (Production Support, Mission Critical Support)
- Volume discounts: Yes
- Tiered pricing: Yes (per-core tiers and ELA structures)
Owner action (Mei) for SKU detail.

### Q53. Pricing strategy 2026-2027
Owner action (Mei).

### Q54. Top 5 sales partners
Owner action (Mei). Likely candidates: Dell Technologies, HPE, Lenovo, Cisco, IBM, Hitachi Vantara, Fujitsu, NEC, AWS, Microsoft, Google Cloud, Oracle, Alibaba.

### Q55. Contract term distribution
Owner action (Yoomi).

### Q56. Client size targeting
Owner action (Yoomi).

### Q57-Q59. Geographic, customer-size, revenue distributions
Owner action (Yoomi).

---

## Market Responsiveness-Record

### Q60. Releases (last 12 months)
Owner action with product input. Anchor: VCF 9.0 GA mid-2025, VCF 9.0.1 patch update, multiple VCF Operations and VCF Automation point releases, vDefend updates, Avi Load Balancer 31.x stream, HCX 4.11, VLR / Live Cyber Recovery updates.

### Q61. Roadmap adaptations
Owner action. Anchor candidates: (1) Aria/vRealize unified into VCF Operations; (2) Private AI Foundation with NVIDIA in response to enterprise GenAI demand; (3) Live Cyber Recovery for ransomware recovery in response to evolving threat landscape.

---

## Marketing Execution

### Q62. Main marketing message (500 char)
Owner action (Yoomi).

### Q63. Marketing strategy and changes
Owner action (Yoomi).

### Q64. Marketing FTEs
Owner action (Yoomi).

---

## Customer Experience

### Q65. CX-linked compensation
Owner action (Yoomi).

### Q66. Top 3 negative CX issues
Owner action (Yoomi).

### Q67. Customer satisfaction methods
Owner action (Yoomi). Historical mix: CAB, NPS surveys, renewal-rate tracking, executive briefings, Gartner Peer Insights, G2.

---

## Operations

### Q68. FTE counts
Owner action (Yoomi/HR).

---

## Market Understanding

### Q69. Top 3 SVP competitors
Owner action (Yoomi). Anchor candidates: Microsoft Azure Stack HCI / Hyper-V; Nutanix Cloud Platform; Red Hat OpenShift Virtualization (KubeVirt).

### Q70. Two future disruptors (24 months)
Owner action (Yoomi).

### Q71. Market evolution (12 months)
Owner action (Yoomi).

### Q72. Top 3 differentiation
Owner action (Yoomi).

### Q73. Why prospects buy/do not buy
Owner action (Yoomi).

### Q74. Key trends through 2029
Owner action (Yoomi).

### Q75. Go-to-market, positioning, messaging
Owner action (Yoomi).

### Q76. Integrations and alliances
- Backup: Veeam, Dell, Commvault, Cohesity, Rubrik, Veritas, IBM, HYCU, Druva
- DR: Veeam, Zerto, Dell, Commvault, plus VLR
- Automation: ServiceNow, Ansible Automation Platform, Terraform, HashiCorp Vault, Jenkins, GitLab
- Observability: Splunk, Datadog, Dynatrace, New Relic, Grafana, Elastic

---

## Sales Strategy

### Q77. Existing accounts vs new logos
Owner action (Yoomi).

### Q78. Pricing changes (12 months)
Owner action (Yoomi).

### Q79. Ideal customer
Owner action (Yoomi).

### Q80. New pricing models
Owner action (Mei).

### Q81. Channel strategy changes
Owner action (Mei).

---

## Offering (Product) Strategy

### Q82. Value proposition (500 char)
Owner action (Yoomi).

### Q83. Top 3 roadmap items (NDA)
Owner action (product).

### Q84. Migration strategy and programs
HCX is the primary migration accelerator from VMware and non-VMware sources (Hyper-V, KVM). VCF Import migrates existing vSphere clusters into VCF without redeploying workloads. Partner-led services from Dell, HPE, IBM Consulting, Accenture, and global SIs provide planning and execution.

### Q85. Top 3 customer requests
Owner action (Yoomi).

### Q86. Major 2026-2027 disruptors
Owner action (Yoomi).

---

## Business Model

### Q87. Business model adaptation through 2027
Owner action (Yoomi).

### Q88. Top 3 business risks and mitigations
Owner action (Yoomi).

---

## Vertical-Industry Strategy

### Q89-Q92. Vertical focus, strategy, targets, growth
Owner action (Yoomi). Strong VCF verticals: financial services, government and defense, telco, healthcare, manufacturing.

---

## Innovation

### Q93. R&D as percent of revenue
Owner action (Yoomi/Finance). Consolidated R&D in Broadcom 10-K.

### Q94. Differentiation investments (12 months)
Owner action (Yoomi).

---

## Geographic Strategy

### Q95-Q97. Geographic focus, growth regions, targets
Owner action (Yoomi). Strong VCF regions: North America, Western Europe, Japan, Australia/New Zealand. Growth focus: India, Southeast Asia, Middle East (sovereignty-driven), Latin America.

### Q98. Region-specific standards and regulations
Anchor: EU NIS2, DORA, AI Act, Cyber Resilience Act, GDPR; US CMMC 2.0 and post-quantum executive orders; Australia Essential Eight; India DPDP Act; Saudi Arabia PDPL; UAE NESA. VCF responds with sovereign cloud reference architectures, regional data residency options, partner-delivered sovereign clouds, and FIPS 140-3 plus Common Criteria validation programs.

---

*Owner-action items above need confidential financial, sales, marketing, or roadmap data from Broadcom internal sources. Product and technical answers (Q1-Q46) are grounded against the project's ChromaDB extraction of VCF 9.0 product documentation; supporting facts cite source PDF, page, and section.*
