<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + CTGW/Edge/T0"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying a K8s Cluster in a Namespace utilizing an "NSX + CTGW/Edge/T0" architecture** inside a vSphere environment.

* **K8s Cluster**
    * [Deployment via vCenter UI](3e1-deployment-k8s.md)
    * [**Deployment via CLI**](#deployment_k8s)
    * [Access via CLI](3e3-access-k8s.md)

</div>

<div markdown>
![VDS Architecture](images/3e1-0-k8s.jpg){ width="100%" }
</div>
</div>

---

## K8s Cluster Deployment {: #deployment_k8s }

![Topology](images/3e2-1-Topology.jpg){ width="60%" style="display: block; margin: 0 auto;" }

??? info ":material-laptop: Client Operating System"
    While the command outputs below are captured from a **Windows client**, the `vcf` and `kubectl` CLI tools operate identically across **Linux** and **macOS** environments.

### Create K8s Cluster

#### Connect to the Supervisor
See [Supervisor Namespace Access](3d2-access-namespace.md#namespaceaccess)


#### Check all K8s Cluster resources

* **Check TKR releases available**
    ```text
    kubectl get kubernetesreleases
    ```

    ??? abstract "Output example"
        <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl get kubernetesreleases</b>
        NAME                                      VERSION                                 READY   COMPATIBLE   CREATED   TYPE
        <snip>
        v1.35.5---vmware.1-vkr.1                  v1.35.5+vmware.1-vkr.1                  True    True         4d8h<
        <snip>
        </code></pre>

* **Check VM Class available**
    ```text
    kubectl get virtualmachineclass
    ```

    ??? abstract "Output example"
        <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl get virtualmachineclass</b>
        NAME                 CPU   MEMORY
        best-effort-medium   2     8Gi
        best-effort-small    2     4Gi
        guaranteed-medium    2     8Gi
        guaranteed-small     2     4Gi
        </code></pre>

* **Check Storage Class availablee**
    ```text
    kubectl get storageclass
    ```

    ??? abstract "Output example"
        <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl get storageclass</b>
        NAME                                                   PROVISIONER              RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
        <snip>
        vsan-default-storage-policy                            csi.vsphere.vmware.com   Delete          Immediate              true                   22h
        <snip>
        </code></pre>


#### Create the K8s Cluster yaml file 
Create file "my-cluster.yaml"

??? info "my-cluster.yaml file"
    ```text
    apiVersion: cluster.x-k8s.io/v1beta2
    kind: Cluster
    metadata:
      name: my-cluster
      namespace: demo-space
    spec:
      clusterNetwork:
        services:
          cidrBlocks: ["10.96.0.0/12"]
        pods:
          cidrBlocks: ["192.168.156.0/20"]
        serviceDomain: "cluster.local"
      topology:
        classRef:
          name: builtin-generic-v3.6.0
        version: v1.35.5---vmware.1-vkr.1
        controlPlane:
          replicas: 3
        workers:
          machineDeployments:
            - class: node-pool
              name: workers
              metadata:
                annotations:
                  cluster.x-k8s.io/cluster-api-autoscaler-node-group-min-size: "3"
                  cluster.x-k8s.io/cluster-api-autoscaler-node-group-max-size: "5"
        variables:
          - name: vmClass
            value: best-effort-small
          - name: storageClass
            value: vsan-default-storage-policy
    ```

#### Deploy the K8s Cluster yaml file 
```text
kubectl apply -f my-cluster.yaml
```

??? abstract "Output example"
    <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl apply -f my-cluster.yaml</b>
    cluster.cluster.x-k8s.io/my-cluster created
    </code></pre>


---

### Validate Deployment

#### Validate K8s Cluster Status
It takes around 5 minutes to get the K8s Cluster ready
```text
kubectl get cluster
```

??? abstract "Output example"
    <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl get cluster</b>
    NAME         CLUSTERCLASS             <b>AVAILABLE</b>   CP DESIRED   <b>CP AVAILABLE</b>   CP UP-TO-DATE   <b>W DESIRED</b>   W AVAILABLE   W UP-TO-DATE   <b>PHASE</b>         AGE    VERSION
    my-cluster   builtin-generic-v3.6.0   <b>True</b>        3            <b>3</b>              3               <b>3</b>           3             3              <b>Provisioned</b>   7m32s   v1.35.5+vmware.1
    </code></pre>

#### Retrieve K8s Cluster Controller VIP
```text
kubectl get cluster my-cluster -o jsonpath='{.spec.controlPlaneEndpoint.host}'
```

??? abstract "Output example"
    <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl get cluster my-cluster -o jsonpath='{.spec.controlPlaneEndpoint.host}'
    10.150.0.8</b>
    </code></pre>

#### Validate K8s Nodes Status
```text
kubectl get machines
```

??? abstract "Output example"
    <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl get machines</b>
    NAME                                   CLUSTER      NODE NAME                              <b>READY</b>   AVAILABLE   UP-TO-DATE   PHASE     AGE     VERSION
    my-cluster-lr2wr-846xb                 my-cluster   my-cluster-lr2wr-846xb                 <b>True</b>    True        True         Running   5m57s   v1.35.5+vmware.1
    my-cluster-lr2wr-jg9d2                 my-cluster   my-cluster-lr2wr-jg9d2                 <b>True</b>    True        True         Running   3m47s   v1.35.5+vmware.1
    my-cluster-lr2wr-mszdm                 my-cluster   my-cluster-lr2wr-mszdm                 <b>True</b>    True        True         Running   109s    v1.35.5+vmware.1
    my-cluster-workers-8nzn6-92xvh-68x8b   my-cluster   my-cluster-workers-8nzn6-92xvh-68x8b   <b>True</b>    True        True         Running   5m47s   v1.35.5+vmware.1
    my-cluster-workers-8nzn6-92xvh-chjkb   my-cluster   my-cluster-workers-8nzn6-92xvh-chjkb   <b>True</b>    True        True         Running   5m47s   v1.35.5+vmware.1
    my-cluster-workers-8nzn6-92xvh-ksg87   my-cluster   my-cluster-workers-8nzn6-92xvh-ksg87   <b>True</b>    True        True         Running   5m47s   v1.35.5+vmware.1
    </code></pre>

