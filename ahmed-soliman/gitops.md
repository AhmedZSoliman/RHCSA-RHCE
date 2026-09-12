# GitOps

**Outlines**

* **What is GitOps?**
* **TOP GITOPS TOOLS | ARCHITECTURE OF ARGO CD | GITOPS**
* **HOW TO INSTALL AND USE ARGO CD EXAMPLES**

## **`GitOps:`**

<figure><img src=".gitbook/assets/image (44).png" alt=""><figcaption></figcaption></figure>

**There is no GitOps, and you’re trying to deploy your application or infrastructure in a Kubernetes cluster. For example, let’s say we want to make a change to a Kubernetes worker node, like adding a taint. Without GitOps, someone might manually update the node configuration. Ten days later, if someone asks what changes were made to the cluster, there is no way to track it. There is no versioning, no auditing, and no clear record of who made the changes.**

**Compare this to source code management: we track changes in a Git repository. Any change goes through a pull request, verification process, and review before merging. Once merged, CI/CD pipelines (like GitHub Actions) build and deploy the application. This provides a proper mechanism for both verification (CI) and deployment (CD).**

**Without GitOps, you might deploy applications using shell scripts, Python scripts, `kubectl`, or Helm. However, there’s no tracking mechanism for what changes were deployed. This is where GitOps comes into play: if your source code has proper tracking, your deployments should too. If your CI pipeline is Git-integrated, your CD pipeline should be as well.**

**Let’s say we use a GitOps tool like ArgoCD. You store your Kubernetes manifest files declaratively in a Git repository. A DevOps engineer submits a pull request to update the manifest files. Another engineer reviews the changes, and once approved, the manifest files are merged into the Git repository. The GitOps controller (e.g., ArgoCD) then automatically picks up the changes and deploys them to the Kubernetes cluster.**

**For example, a DevOps engineer updates `deploy.yml` in the manifests. They submit a pull request, another engineer verifies it, and once merged, ArgoCD deploys it to the cluster. GitOps solves the problem of tracking deployments.**

**GitOps is not just about application delivery—it’s also about infrastructure management. When you have hundreds of Kubernetes clusters, managing the infrastructure becomes even more critical than managing the applications themselves. If you have 100 applications across 100 clusters, you’re managing thousands of resources. GitOps provides a proper way to manage this infrastructure at scale.**

**Using GitOps, you can easily deploy an application to any Kubernetes cluster with full tracking and versioning. It goes beyond traditional DevOps practices: building a CI/CD platform requires attention not just to CI but also to CD. In practice, we’ve implemented a CI/CD example using Jenkins for CI and ArgoCD for CD to demonstrate an end-to-end platform**.

<figure><img src=".gitbook/assets/image (45).png" alt=""><figcaption></figcaption></figure>

**Whatever you see in a GitHub repo is the same configuration deployed on your Kubernetes cluster.**

**When you track resources in Git, your changes are versioned and immutable. Whenever you make any change, there is a GitOps controller continuously watching the changes in your Kubernetes manifest files and deploying them to the cluster.**

**Not just Git is used for GitOps, but you can integrate it with, for example, an S3 bucket. S3 can be versioned and can store declarative manifests.**

**GitOps can be used with different other solutions.**

**The changes have to be pulled automatically.**

**Let’s say you build a GitOps tool. These are the principles you have to follow.**

**Let’s say someone goes to your Kubernetes cluster and modifies a resource directly. The GitOps controller will say no — it will not allow this change because Git is the single source of truth. If you want to make any change to a resource that the GitOps controller is managing, you need to put the change in the Git repo, and only then will it be deployed by the controller.**

**Nobody expects the GitOps controller to manage your entire Kubernetes cluster.**

**GitOps continuously watches your Kubernetes cluster, keeping track of all resources. It gets info from the cluster and the Git repo, compares both states, continuously monitors the differences, and applies changes according to the Git repo because it’s the single source of truth.**

<figure><img src=".gitbook/assets/image (46).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (47).png" alt=""><figcaption></figcaption></figure>

**Prevent any unwanted changes.**

**GitOps is not actually related to Git, but to the versioning concept.**

**you have automatic upgrades using a pull mechanism or push.**

## **`TOP GITOPS TOOLS | ARCHITECTURE OF ARGO CD | GITOPS`**

<figure><img src=".gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (49).png" alt=""><figcaption></figcaption></figure>

**Maintain sync and keep the state between Git and K8s so that Git acts as the single source of truth, ensuring no one can apply any unwanted configurations.**

**In a traditional deployment or continuous delivery model, you don’t get this auto-healing feature with the shell scripts used in your Jenkins pipeline.**

**A GitOps tool like Argo CD ensures the state is continuously maintained between Git and the K8s cluster.**

<figure><img src=".gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

**There is Git and there is Kubernetes (K8s). If you’re trying to build a GitOps tool, you have to maintain the state between Git and K8s. One microservice gets the state from Git, and another microservice gets the state from K8s. Then, the application controller retrieves the state from Git and compares it with the K8s state. If they are not the same, the application controller will sync the cluster with the Git repository.**

**As an end user, you want to interact with a GitOps application in a way similar to Jenkins. For example, with Jenkins, you have a UI or CLI. The same applies to Argo CD—you can use the UI or CLI. In the case of Argo CD, if a user tries to connect via the UI or CLI, they communicate through the API server.**

**The API server is used by users to communicate with Argo CD through the CLI or GUI. It handles authentication, such as single sign-on (SSO), and can integrate with existing OIDC providers. The API server can also fetch information from external sources. When you install Argo CD, it comes with Dex, a lightweight OIDC service. You can consider Dex as a proxy server that connects to any of your existing identity providers and provides SSO capabilities for the API server.**

**Finally, you have Redis, which is used for caching information—it stores the current state of the cluster. The application controller is deployed as a StatefulSet.**

**Between these two lines is the architecture of GitOps tools.**

**There are three microservices in the GitOps application that form the underlying system for Argo CD.**

<figure><img src=".gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

## **`How to install and use Argocd`**

[https://argo-cd.readthedocs.io/en/stable/getting\_started/](https://argo-cd.readthedocs.io/en/stable/getting_started/)

When you install a Kubernetes controller, **each controller should be in its own namespace** with its name. This keeps things **separate**, avoids conflicts with other controllers, and helps with **organization** — each controller works like it is in its own space.

The **manifest files** you use to install Argo CD deploy all the Argo CD components **inside the `argocd` namespace**.

Check the pods:

`kubectl get pods -n argocd --watch`

If you want to **use the Argo CD UI**, first check the services:

`kubectl get svc -n argocd`

The service is usually **ClusterIP**, which you cannot access from outside the cluster. In this demo, we **change it to NodePort** so you can open it in your browser:

`kubectl edit svc argocd-server -n argocd`

Change `type: ClusterIP` to `type: NodePort`.

For **Minikube (local)**, you can also use **port forwarding**:

`minikube service list -n argocd`

Minikube will create a **tunnel** and give you an **IP address**. Then run:

`minikube service argocd-server -n argocd`

Finally, **copy the URL and open it in your browser** to access Argo CD.















