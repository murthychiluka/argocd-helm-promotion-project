
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



## Installing ArgoCD CLI
---------------------
https://argo-cd.readthedocs.io/en/stable/cli_installation/
Steps For Webinar
-----------------
Deploy ArgoCD using below url
-----------------------------
kubectl apply -n argocd --server-side -f argocd.yaml

Login to argocd
---------------
argocd login <argocd_url> --username <username> --password <password>
argocd login localhost:8080 --username admin --password Rop-qszxftoG9aBl

Adding cluster to argocd
------------------------
argocd cluster add <cluster_context> --name <cluster_name>
argocd cluster add cloud_user@uat-test.us-east-1.eksctl.io --name uat-test

To list clusters of argocd
--------------------------
argocd cluster list

Connect git repo with argocd
----------------------------
Settings -> Repositories -> Connect Repo

Deploy root-app.yaml file from argocd-helm-promotion-proj repo
--------------------------------------------------------------
kubectl apply -f root-app.yaml

Note : You need to update the prod cluster name in prod application.yaml
Argocd Architecture & Components
--------------------------------
ArgoCD Server
This is the api server of ArgoCD that exposes ArgoCD rest API's. We will interact with ArgoCD through this API's through UI, CLI & CICD tools.

ArgoCD Controller
This is the controller that reads the custom resource "Application" when we deploy to kubernetes cluster(Which contains a pointer to Kubernetes manifest files). It fetches Kubernetes manifest files and deploys to the Kubernetes cluster.

ArgoCD Repo Server
Repo Server is the service that interacts with Git repositories and clones the manifest files.
Application Controller will request the Repo Server to clone manifests. Repo Server clones them and puts them in Redis cache for 24 hours.
The Repo Server polls Git every 3 minutes by default to check for new changes.

ArgoCD Dex Server
ArgoCD Dex Server is an OIDC provider for ArgoCD, which helps us to authenticate and authorize with external identity providers.
Shiva
Argocd Architecture & Components -------------------------------- ArgoCD Server This is the api serv...
