# Deploy the MuShop Application

## Introduction

There are four options for deploying MuShop. They range from manual (docker), automated (Helm) to fully automated (Terraform).

![MuShop Deployment](media/c9d20eebc1e88c7b2ac13693fdececc1.png)

Designing in microservices offers excellent separation concerns and provides developer independence. While these benefits are clear, they can often introduce some complexity for the development environment. Services support configurations that offer flexibility, when necessary, and establish parity as much as possible. It is essential to use the same tools for development to production.

![MuShop Deployment](media/399b6054ff0385e9ba4ed430552c4404.png)  
*Note: This diagram contains services not covered by these labs.*

Estimated Lab Time: 30 minutes

### Objectives

In this lab, you will:

-   Gather Cloud Information
-   Download Source Code
-   Setup Kubernetes Cluster on OKE
-   Deploy with Helm
-   Expose your app publicly
-   Explore under the Hood

### Prerequisites

-   An Oracle Free Tier(Trial), Paid or LiveLabs Cloud Account
-   Completed the **Setup Cloud Environment** lab

Collapse All Tasks

## Task 1: Obtain MuShop source code

1.  Open up Cloud Shell and clone the github repo.

    Copygit clone https://github.com/oracle-quickstart/oci-cloudnative.git mushop

    Sample response:

    Cloning into 'mushop'...

    remote: Enumerating objects: 542, done.

    remote: Counting objects: 100% (542/542), done.

    remote: Compressing objects: 100% (313/313), done.

    remote: Total 15949 (delta 288), reused 424 (delta 200), pack-reused 15407

    Receiving objects: 100% (15949/15949), 17.59 MiB \| 33.71 MiB/s, done.

    Resolving deltas: 100% (9557/9557), done.

2.  Change to the mushop directory

    Copycd mushop

    ![MuShop Tree](media/2026f7852b472fa0091c3c351e19fdb4.png)

    *./deploy:* Collection of application deployment resources  
    *./src:* MuShop individual service code, Dockerfile, etc

3.  Check **kubectl** context

    Copykubectl config current-context

    Sample response:

    cluster-c4daylfgvrg

4.  Create a namespace for MuShop App (microservices will reside on this namespace)

    Copykubectl create namespace mushop

    Sample response:

    namespace/mushop created

5.  Set the default **kubectl** namespace to skip adding **--namespace mushop** to every command. You can replace *mushop* with *your name*.

    Copykubectl config set-context --current --namespace=mushop

## Task 2: Cluster Setup for the App with Helm

MuShop provides an umbrella helm chart called setup, which includes several recommended installations on the cluster. These installations represent common 3rd party services, which integrate with Oracle Cloud Infrastructure or enable certain application features.

| Table 1: MuShop: Cloud Native Polyglot microservices App \| Lab 2: Deploy the Microservices App            |                                                         |                        |
|------------------------------------------------------------------------------------------------------------|---------------------------------------------------------|------------------------|
| **Chart**                                                                                                  | **Purpose**                                             | **Option**             |
| [Prometheus](https://github.com/helm/charts/blob/master/stable/prometheus/README.md)                       | Service metrics aggregation                             | prometheus.enabled     |
| [Grafana](https://github.com/helm/charts/blob/master/stable/grafana/README.md)                             | Infrastructure/service visualization dashboards         | grafana.enabled        |
| [Metrics Server](https://github.com/helm/charts/blob/master/stable/metrics-server/README.md)               | Support for Horizontal Pod Autoscaling                  | metrics-server.enabled |
| [Ingress Nginx](https://kubernetes.github.io/ingress-nginx/)                                               | Ingress controller and public Load Balancer             | ingress-nginx.enabled  |
| [Service Catalog](https://github.com/kubernetes-sigs/service-catalog/blob/master/charts/catalog/README.md) | Service Catalog chart utilized by Oracle Service Broker | catalog.enabled        |
| [Cert Manager](https://github.com/jetstack/cert-manager/blob/master/README.md)                             | x509 certificate management for Kubernetes              | cert-manager.enabled   |

1.  Create a namespace for MuShop utilities

    Copykubectl create namespace mushop-utilities

    Sample response:

    namespace/mushop-utilities created

2.  Install cluster dependencies using helm:

    Copyhelm dependency update deploy/complete/helm-chart/setup

    Sample response:

    Hang tight while we grab the latest from your chart repositories...

    ...Successfully got an update from the "stable" chart repository

    Update Complete. ⎈Happy Helming!⎈

    Saving 7 charts

    Downloading prometheus from repo https://kubernetes-charts.storage.googleapis.com

    Downloading grafana from repo https://kubernetes-charts.storage.googleapis.com

    Downloading metrics-server from repo https://kubernetes-charts.storage.googleapis.com

    Downloading ingress-nginx from repo https://kubernetes.github.io/ingress-nginx

    Downloading catalog from repo https://svc-catalog-charts.storage.googleapis.com

    Downloading cert-manager from repo https://charts.jetstack.io

    Downloading jenkins from repo https://kubernetes-charts.storage.googleapis.com

    Deleting outdated charts

3.  Install setup helm chart:

    Copyhelm install mushop-utils deploy/complete/helm-chart/setup --namespace mushop-utilities

*Note:* When you install the mushop-utils chart release, Kubernetes will create an OCI LoadBalancer to the be used by the ingress kubernetes.

## Task 3: Get Ingress IP Address

Part of the cluster setup includes the installation of an nginx ingress controller. This resource exposes an OCI load balancer, with a public ip address mapped to the OKE cluster.

By default, the mushop helm chart creates an Ingress resource, routing ALL traffic on that ip address to the svc/edge component.

1.  Locate the EXTERNAL-IP assigned to the ingress controller:

    Copykubectl get svc mushop-utils-ingress-nginx-controller --namespace mushop-utilities

    Sample response:

    NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE

    mushop-utils-ingress-nginx-controller LoadBalancer 10.96.150.230 129.xxx.xxx.xxx 80:30195/TCP,443:31059/TCP 1m

2.  Explore the cluster services deployments:

    Copykubectl get deployments --namespace mushop-utilities

## Task 4: Deploy the eCommerce App with Helm

Remembering that helm provides a way of packaging and deploying configurable charts, next we will deploy the application in "mock mode" where cloud services are mocked, yet the application is fully functional

1.  Deploy the application in "mock mode" where cloud services are mocked, yet the application is fully functional

    Copyhelm install mushop deploy/complete/helm-chart/mushop --set global.mock.service="all"

2.  Please be patient. It may take a few moments to download all the application images.

    Copykubectl get pods --watch

    *Note:* To leave the *watch* press CTRL-C anytime. If do not want to keep watching and just see the current list of PODS, just use kubectl get pods

3.  After inspecting the resources created with helm install, launch the application in your browser using the **EXTERNAL-IP** from the nginx ingress.
4.  Find the EXTERNAL-IP assigned to the ingress controller. Open the IP address in your browser.

    Copykubectl get svc mushop-utils-ingress-nginx-controller --namespace mushop-utilities

5.  Open to the MuShop Storefront by using your browser connecting to http://\< EXTERNAL-IP \>

    ![MuShop Storefront](media/b5a7bedca1227e6b1e4c6598c2bf128e.png)

## Task 5: Explore the deployed app

When you create a Deployment, you'll need to specify the container image for your application and the number of replicas that you want to run.

Kubernetes created a Pod to host your application instance. A Pod is a Kubernetes abstraction that represents a group of one or more application containers (such as Docker), and some shared resources for those containers. Those resources include:

-   Shared storage, as Volumes
-   Networking, as a unique cluster IP address
-   Information about how to run each container, such as the container image version or specific ports to use

The most common operations can be done with the following kubectl commands:

-   **kubectl get** - list resources
-   **kubectl describe** - show detailed information about a resource
-   **kubectl logs** - print the logs from a container in a pod
-   **kubectl exec** - execute a command on a container in a pod

You can use these commands to see when applications were deployed, what their current statuses are, where they are running and what their configurations are.

1.  Check the microservices deployments for MuShop

    Copykubectl get deployments

    *Note:* You should use kubectl get deployments --namespace mushop if you didn't set *mushop* as default namespace

2.  Check the pods deployed

    Copykubectl get pods

3.  Get the last created pod to inspect
4.  Copyexport POD_NAME=\$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\\n"}}{{end}}'\|awk '{print \$1}'\|tail -n 1) && \\

    echo Using Pod: \$POD_NAME

5.  View what containers are inside that Pod and what images are used to build those containers

    Copykubectl describe pod \$POD_NAME

6.  Anything that the application would normally send to STDOUT becomes logs for the container within the Pod. We can retrieve these logs using the kubectl logs command:

    Copykubectl logs \$POD_NAME

7.  Execute commands directly on the container once the Pod is up and running.

    Copykubectl exec \$POD_NAME env

8.  List the content of the Pod’s container work folder:

    Copykubectl exec -ti \$POD_NAME ls

    *Note:* You can also start a bash session on the Pod's container, just change the ls to bash. Remember that you need to type exit to exit the bash session.

## Task 6: Under the Hood

1.  To get a better look at all the installed Kubernetes manifests by using the template command.
2.  Copy
3.  mkdir ./out
4.  Copy
5.  helm template mushop deploy/complete/helm-chart/mushop --set global.mock.service="all" --output-dir ./out
6.  Explore the files, and see each output.

You may now proceed to the next lab.

## Learn More

-   [MuShop Github Repo](https://github.com/oracle-quickstart/oci-cloudnative)
-   [MuShop Deployment documentation](https://oracle-quickstart.github.io/oci-cloudnative/cloud/)
-   [Terraform Deploymment scripts](https://github.com/oracle-quickstart/oci-cloudnative/tree/master/deploy/complete/terraform)
-   Full Solution deployment with one click - launches in OCI Resource Manager directly<https://console.us-ashburn-1.oraclecloud.com/resourcemanager/stacks/create?region=home&zipUrl=https://github.com/oracle-quickstart/oci-cloudnative/releases/latest/download/mushop-stack-latest.zip>

## Acknowledgements

-   **Author** - Adao Junior
-   **Contributors** - Kay Malcolm (DB Product Management), Adao Junior
-   **Last Updated By/Date** - Adao Junior, October 2020
