
### Disadvantages of Traditional Push-Based CI/CD

1. **Cluster changes may not be tracked in Git**

   If someone manually changes resources directly in the Kubernetes cluster, those changes may not be reflected in the Git manifest files.

   This can cause **configuration drift** between Git and the actual cluster state.

   Example:

   ```text
   Git:
   replicas: 3

   Kubernetes cluster:
   replicas: 5
   ```

   Git no longer represents the actual desired configuration of the cluster.

2. **Security risk**

   CI tools such as Jenkins or GitHub Actions need credentials/identity with permission to deploy to the Kubernetes cluster.

   If those credentials are compromised, an attacker could potentially gain access to the cluster and perform unauthorized actions.

   ```text
   Jenkins
      |
      | Kubernetes credentials
      ↓
   Kubernetes Cluster
   ```

   The more permissions given to the CI tool, the greater the potential impact if its credentials are compromised.

3. **Limited awareness of the application's actual state**

   The CI tool mainly performs the deployment. After deploying, it may not continuously monitor and reconcile the application against the desired state.

   For example, the deployment may succeed, but later:

   * a pod may crash,
   * someone may manually change the deployment,
   * a resource may be deleted,
   * the application may become unhealthy.

   The CI pipeline itself does not continuously ensure that the cluster matches the desired state.

4. **No automatic reconciliation**

   In a traditional push-based model, the CI tool typically pushes the desired configuration to the cluster during deployment.

   If the cluster later deviates from the desired configuration, the CI tool does not necessarily detect and correct the difference automatically.

   This is one of the major advantages of the GitOps model.
