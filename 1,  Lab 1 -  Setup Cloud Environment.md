# Setup Cloud Environment

## Introduction

You will take on the persona of an Operations Engineer. You will initiate the Oracle cloud environment that will be used to create and deploy your microservices applications. This environment will be contained within a cloud Compartment, and communication within the Compartment will be via a Virtual Cloud Network (VCN). The Compartment and VCN will isolate and secure the overall environment. You will deploy the Oracle Cloud Infrastructure Container Engine for Kubernetes(OKE).

Estimated time: 20 minutes

### Objectives

-   Log into OCI Tenancy.
-   Setup Oracle Cloud Infrastructure (OCI) components.

**We recommend that you create a notes page to write down all of the credentials you will need.**

### Prerequisites

-   Your Oracle Cloud Trial Account
-   You have already applied for and received your Oracle Cloud Free Tier Account.

Collapse All Tasks

## Task 1: Log into OCI Tenancy

Log in to your OCI dashboard and retrieve information required to create resources.

1.  Once you log in you will see a page similar to the one below. Click on "Infrastructure Dashboard."

    ![Landing Screen](media/7515ea1a70b78c2bc20b6fc01e70db59.png)

## Task 2: Basic OCI Infrastructure Setup

1.  Click the **Navigation Menu** in the upper left, navigate to **Identity & Security** and select **Compartments**.

    ![Compartment](media/7eb66ce1a27cf25ef4b05ff8570c1703.png)

2.  From this screen, you will see a list of compartments, click **Create Compartment**.

    ![Compartment Screen](media/86fe43e722037f9bf3b17f88e5180246.png)

3.  Enter the following:
    -   Name: Enter **"AppDev".**
    -   Description: Enter a description (required), for example: "AppDev compartment for the getting started tutorial". Avoid entering confidential information.
    -   Parent Compartment: Select the compartment you want this compartment to reside in. Defaults to the root compartment (or tenancy).
    -   Click **Create Compartment**.
    -   Your compartment is displayed in the list.

        ![AppDev Compartment](media/b0771dfa9533912c66dfef5c07eb688b.png)

4.  Click the Cloud Shell icon in the Console header. Note that the OCI CLI running in the Cloud Shell will execute commands against the region selected in the Console's Region selection menu when the Cloud Shell was started.

    ![CloudShell](media/248fe37c0469ea38e092ce1dd33218d1.png)

    ![CloudShell](media/58409088a41359e780f84b5031eb881d.png)

Now you are ready to move on to Step 3.

## Task 3: Create OKE Kubernetes Cluster

1.  Click the **Navigation Menu** in the upper left, navigate to **Developer Services**, and select **Kubernetes Clusters (OKE)**.

    ![OKE](media/99efe53fd03298c3c57aeffcefb05eae.png)

2.  Verify you are in the **AppDev** Compartment and click **Create Cluster**.

    ![Compartment](media/c39d120ec6cdecec40b91dbeeda9b489.png)

3.  Choose Quick Create as it will create the new cluster along with the new network resources such as Virtual Cloud Network (VCN), Internet Gateway (IG), NAT Gateway (NAT), Regional Subnet for worker nodes, and a Regional Subnet for load balancers. Select **Launch Workflow**

    ![Quick Create Cluster](media/a0858269f21cfa508838ac4dce620277.png)

4.  Keep the name to **cluster1** and the other default values, click Next to review the cluster settings. Do not choose the Kubernetes API Endpoint to be *Private Endpoint* or you will need a bastion or an admin instance to access the kubectl commands. You can explore Private Endpoint on an specific lab. *Optionally choose visibility of the Kubernetes Worker Nodes to Public Workers if you want public access to your nodes and number of nodes to 2 or 1 if you want reduced number of nodes*

Keep the name to cluster1 and the other default values, click Next to review the cluster settings Optionally choose visibility type to Private Endpoint to host your Kubernetes endpoint in a private subnet. Also choose visibility type to Public Workers if you want public access to your nodes. You can also change the number of nodes to 2 or 1 if you want to reduce the number of nodes.

![Cluster Details](media/fdb56a05b1bd93dd17a4d4424f17dafc.png)

1.  Review the the Cluster Creation and then select **Create Cluster**.

    ![Cluster Info](media/8346768923cdbcbe475898706414c271.png)

2.  Once launched it should usually take around 5-10 minutes for the cluster to be fully provisioned and display an Active.

## Task 4: Setup OKE Kubernetes Cluster Cloud Shell Access

1.  On the **Clusters** view, select the just created cluster and then click on the **Access Cluster** button.

    ![Access Cluster](media/6298f6654dd26ef0fc32ba54915daa0f.png)

2.  Leave the **Cloud Shell Access** selected. If Cloud Shell is not already open, **Launch Cloud Shell**, copy the oci cli command to create the kubeconfig and paste on the Cloud Shell Terminal.

    ![Access Cluster](media/738fe9e7cd29904b2cc5447994a1189c.png)

    ![Access Cluster](media/c08447a60006b3db9c429b32583dfba5.png)

3.  Check if you have access to your cluster with kubectl.

    Copykubectl get cs

    NAME STATUS MESSAGE ERROR

    scheduler Healthy ok

    controller-manager Healthy ok

    etcd-0 Healthy {"health":"true"}

4.  Check the version of your kubectl client and kubernetes server with kubectl.

    Copykubectl version

5.  Get the nodes and check if they are *Ready* with kubectl.

    Copykubectl get nodes

    NAME STATUS ROLES AGE VERSION

    10.0.10.2 Ready node 1m v1.19.7

    10.0.10.3 Ready node 1m v1.19.7

    10.0.10.4 Ready node 1m v1.19.7

You may now proceed to the next lab.
