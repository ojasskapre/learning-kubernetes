## Course Highlights:
1. Foundations of Containers:
Grasped the underpinnings of containers, laying a solid groundwork for the subsequent Kubernetes modules.
Diving into Kubernetes Components:
2. Clusters and Nodes: Understood the architectural elements and the hierarchical significance of clusters and nodes.
- Control Plane: Explored the core components that ensure cluster orchestration, including:
  - etcd: Consistent and highly-available key-value store used for configuration data.
  - API Server: Serves the Kubernetes API, the gateway to interact with the cluster.
  - Scheduler: Responsible for determining the best node for a Pod.
  - Controller Manager: Oversees the Controllers.
  - Cloud Controller Manager: Links Kubernetes to cloud provider-specific APIs.
- Worker Nodes and their Components: Delved into the mechanisms that allow Pods to run on nodes:
  - kubelet: Ensures the containers are running in a Pod.
  - kube-proxy: Maintains network rules for Pod communication.
  - controller runtime: Framework to build Kubernetes controllers.

3. Kubernetes Manifests with YAML:
Comprehended the structure and components of Kubernetes YAML manifests.
Engaged in hands-on exercises, both reading and writing YAML files.

4. Exploring Kubernetes Resources: 
- Workload Resources: Understood and practiced with resources such as Deployment, ReplicaSet, StatefulSet, DaemonSet, and Jobs.
- Service Resources: Grasped networking with ClusterIP, NodePort, and LoadBalancer.
- Storage and Configuration: Delved into persistent storage with Persistent Volumes and configuration management using ConfigMaps and Secrets.

5. Deep Dive into Pods:
Learned the essence of Kubernetes' foundational unit: the Pod.
Troubleshot common issues, inspecting event logs for root cause analysis.
Deployed utility Pods like BusyBox for validation tasks.
Employed kubectl to retrieve and interpret application logs.

6. Networking in Kubernetes:
Successfully assigned an IP address to applications using the LoadBalancer service, ensuring external accessibility.

7. Resource Management:
Understood setting CPU and memory requests/limits, guaranteeing optimal resource allocation and enhanced stability across the cluster.

8. Securing Kubernetes Deployments:
Understood basic security context settings, including privilege escalation, access control (runAsNonRoot), and Pod security policies (readOnlyFileSystem).