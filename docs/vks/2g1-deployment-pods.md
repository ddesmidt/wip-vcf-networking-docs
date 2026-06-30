<h1>
  <img src="../../assets/VKS.png" style="height:30px; vertical-align:middle;"> Supervisor with "NSX + DTGW/VNA"
</h1>

<div class="grid" markdown style="grid-template-columns: 60% 40%">

<div markdown>

This section describes the procedures for **deploying an application (VMs/K8s) into the VKS Namespace with "NSX + DTGW/VNA"** within a vSphere environment.

* Deploy App (VMs)
    * [via vCenter UI](2f1-deployment-vms.md)
    * [via CLI](2f2-deployment-vms.md)
* **Deploy App (K8s)**
    * [**via CLI**](#deployment_pods)
</div>

<div markdown>
![VDS Architecture](images/2g1-0-k8s.jpg){ width="100%" }
</div>
</div>

---

## Deploy App (K8s) {: #deployment_pods }

![Topology](images/2g1-1-Topology.jpg){ width="55%" style="display: block; margin: 0 auto;" }

??? info ":material-laptop: Client Operating System"
    While the command outputs below are captured from a **Windows client**, the `vcf` and `kubectl` CLI tools operate identically across **Linux** and **macOS** environments.

### Deploy a Full Application (Load Balancer + Pods)

#### Connect to the K8s Cluster  
See [Connect to the K8s Cluster](2e2-deployment-k8s.md#connect_k8s)

#### (Optional) Create a Namespace in the K8s Cluster  
Create a Namespace if you want to deploy the application (pod) in a specific K8s Cluster Namespace (not default).  
```text
kubectl create namespace ns1
```  

??? info "Output example"
    <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl create namespace ns1</b>
    namespace/ns1 created
    </code></pre>
#### Allow the deployment of applications in the Namespace.  
```text
kubectl label ns [default|namespace] pod-security.kubernetes.io/enforce=baseline
```  

??? info "Output example"
    <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl label ns ns1 pod-security.kubernetes.io/enforce=baseline</b>
    namespace/ns1 labeled
    </code></pre>

#### Create application (LB + 2 Pods apache) yaml file  
Create file "apache-k8s.yaml"

??? info "apache-k8s.yaml file"
    ```text
    apiVersion: v1
    kind: Service
    metadata:
      name: apache-vip-service
      namespace: ns1
    spec:
      type: LoadBalancer
      ports:
        - port: 80
          targetPort: 8080
          protocol: TCP
      selector:
        app: apache-web
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: apache-deployment
      namespace: ns1
    spec:
      replicas: 2
      selector:
        matchLabels:
          app: apache-web
      template:
        metadata:
          labels:
            app: apache-web
        spec:
          containers:
            - name: apache
              image: public.ecr.aws/docker/library/httpd:2.4
              ports:
                - containerPort: 8080
                  protocol: TCP
              # Intercept startup to write the file, THEN start Apache
              command: ["/bin/sh", "-c"]
              args: 
                - 'sed -i "s/Listen 80/Listen 8080/g" /usr/local/apache2/conf/httpd.conf && echo "<h1>It works - Pod: $HOSTNAME</h1>" > /usr/local/apache2/htdocs/index.html && httpd-foreground'
    ```

#### Deploy the application (LB + 2 Pods apache) 
```text
kubectl apply -f apache-k8s.yaml
```

??? info "Output example"
    <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl apply -f apache-k8s.yaml</b>
    service/apache-vip-service created
    deployment.apps/apache-deployment created
    </code></pre>

---

### Validate deployment of the application (LB + 2 Pods apache) 
* **Check application VIP**  
```text
kubectl get service apache-vip-service -n ns1
```

    ??? info "Output example"
        <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl get service apache-vip-service -n ns1</b>
        NAME                 TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
        apache-vip-service   LoadBalancer   10.107.238.63   10.1.7.138    80:31147/TCP   53s
        </code></pre>


* **Check application Pods**  
```text
kubectl get pods -n ns1
```
    
    ??? info "Output example"
        <pre><code>PS C:\Users\Administrator\Documents> <b>kubectl get pods -n ns1</b>
        NAME                                 READY   STATUS    RESTARTS   AGE
        apache-deployment-58bf9564f6-c8gqc   1/1     Running   0          28s
        apache-deployment-58bf9564f6-dpmcd   1/1     Running   0          28s
        </code></pre>

---

### Access the application (LB + 2 Pods apache) 
```text
curl http://10.1.7.138
```

??? info "Output example"
    <pre><code>PS C:\Users\Administrator\Documents> <b>curl http://10.1.7.138</b>
    &lt;h1&gt;It works - Pod: <b>apache-deployment-58bf9564f6-dpmcd</b>&lt;/h1&gt;
    &nbsp;
    PS C:\Users\Administrator\Documents> <b>curl http://10.1.7.138</b>
    &lt;h1&gt;It works - Pod: <b>apache-deployment-58bf9564f6-c8gqc</b>&lt;/h1&gt;
    </code></pre>
    
    The external load balancer (NSX) load balances (round-robin mode) to the Worker Nodes. Then, each Worker Node load balances to the Apache pods (random). So, to validate the load balancing to the different Apache pods, run the `curl` command multiple times.
