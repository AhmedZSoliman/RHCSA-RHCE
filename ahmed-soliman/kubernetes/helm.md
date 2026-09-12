# Helm

* Helm is a pkg Manager (tool) that simplifies app deployment in k8s.
* It’s like yum or apt pkg manager for the OS that they use to manage app pkg.
* Collect YAML files as a single pkg and publish it on a registry, whether private or public.
* Let’s imagine that we want to deploy an application on a k8s cluster.
* So we need k8s YAML files like service, deployment, secret, PVC, PV, etc.
* Doing these files manually because:
  * First, you will write these YAML files.
  * Second, test them to know if they work as expected or not.
  * Third, deploy it on the cluster.
* Someone has to write these YAML files once and package them as an app.
* Then publish this app in one central place, so people who have something like it\
  can deploy it instead of reinventing the wheel.
* Helm chart: collections of YAML files that package them together as an app.
* Example: Prometheus—creating YAML files from scratch is complex.
* So we will look for someone who did it before, like Helm charts or templates.
* Sharing these Helm charts is very useful.
* Always look for a Helm chart for any app you want to deploy in order to save your time.
* Searching for charts:
  * Public registry (GitHub for the charts)
  * helm search keyword
  * Helm Hub
  * Repos that have these Helm charts
* Install pkg → pull the chart from repo → create release
* Release is a running instance of the chart.
* In companies, we create these charts from scratch and push them to a private registry.
* Anyone in our company can use them.
* Helm as a templating engine.
* Let’s imagine that we have a multiple microservice application.
* Deploying these microservices means you will need YAML files.
* If you have around ten microservices:
  * Each one has the same configuration
  * But a different name and image
  * Repeating the same process ten times is hard
* If the same YAML file needs to be applied multiple times with small differences,
  * Why don’t we use a template?

<figure><img src="../.gitbook/assets/image (59).png" alt=""><figcaption></figcaption></figure>

* In the template, define variables for the parameters that will be different.
* So Helm will know the values needed each time you want to apply the file.
* **values.yaml**:
  * The file that will have the values
  * Or you can set the values through the command line
* Inside the **values.yaml**,
  * You will define the objects that will set values for the variables

<figure><img src="../.gitbook/assets/image (60).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (61).png" alt=""><figcaption></figcaption></figure>

* Helm will apply these YAML files in a k8s cluster by using the template files with **values.yaml**.
* Helm will generate the **manifest files**.
* It will send the manifest files to Kubernetes and tell Kubernetes to apply them.
* **Helm installation:**
  * Official docs: [https://helm.sh/docs/intro/install/](https://helm.sh/docs/intro/install/)
  * Check Helm commands: `helm --help`
* **Important commands:**
  * `helm create --help`
    * It will create a chart with a default folder structure, with empty files or default values
    * Example: `helm create simple-chart`
  * To explore the chart:
    * `cd simple-chart`
    * `find` → to see the folder structure

<figure><img src="../.gitbook/assets/image (63).png" alt=""><figcaption></figcaption></figure>

* There are different sources for the files; you don’t have to make any changes in the YAML file.
* Example: `replicas: {{ .Values.replicacount }}`
  * This field `replicacount` is getting its value from **values.yaml**
* **Helper.tpl**:
  * Shows where the values are getting picked from

<figure><img src="../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

* Helm has simple commands that generate a value for a variable in template files.
* Anything that starts with your chart name will come from **helper.tpl** file.
* Values can also come from **Chart.yaml**.
* **Two different versions in Chart.yaml:**
  * **chart version:**
    * Version of the chart bundled together
    * Increment this version whenever you make changes to the chart itself
  * **app version:**
    * Increment this version whenever you make changes to the application itself

<figure><img src="../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

* There are **two different versions**:
  * **Chart version:**
    * Version of the chart bundled together
    * Increment this version whenever you make changes **to the chart itself**
  * **App version:**
    * Increment this version whenever you make changes **to the application itself**

<figure><img src="../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

* Install an application from a specific Helm chart, like **ArgoCD**.
* Always install **official, verified packages** from **Helm Hub**.

<figure><img src="../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

* This repo contains different URLs for **ArgoCD installation**.
* Add the Helm repo:
  * `helm repo add <reponame> <repo-url>`
  * This repo may have multiple URLs for ArgoCD installation.
* Install the chart:
  * `helm install <release-name> <repo/helmchart-name> --version 7.8.10`
  * This will install the package on your Kubernetes cluster.
* `helm list`
  * Shows the releases you have.
  *

      <figure><img src="../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>
  * **Revisions** will be 1 if you haven’t made any changes.
* Upgrade a Helm chart to a newer version:
  * `helm upgrade <release-name> <repo-name>/<chart-name>`
* To execute a Helm chart that exists on your machine:

<figure><img src="../.gitbook/assets/image (69).png" alt=""><figcaption></figcaption></figure>

* Extract the chart if needed: `tar -xvzf argocd`
  * Be inside the Helm chart directory
    * Install the chart: `helm install <release-name> ./chart-name`
    * Rollback any changes:
    * `helm rollback <release-name>`
* Search for charts in Helm Hub:
  * `helm search hub argo`
    * Result: Many Argo charts, some from official repos, others not
  * `helm search hub argo-cd`

