+++
categories = ["AZ-203", "Azure", "kubernets", "az-cli", "en"]
date = 2019-06-03T00:31:00Z
description = ""
draft = false
image = "https://res-3.cloudinary.com/dgoa5llvh/image/upload/q_auto/az-kubctl-cover.png"
slug = "azure-kubernets-az-cli"
tags = ["AZ-203", "Azure", "kubernets", "az-cli", "en"]
title = "Azure Kubernets - az cli"

+++


Steps to create Azure Kubernet Services (AKS) though azure cli tool.

#####Demo 1

    // Create resource group
    az group create --name kbgroup2 --location eastus

    // Create AKS cluster
    az aks create \
     --resource-group kbgroup2 \
     --name kbcluster2 \
     --node-count 1 \
     --enable-addons monitoring \
     --generate-ssh-keys

###Connect to the cluster
    #if you're in  Azure Cloud Shell skip this step
    az aks install-cli

    #getting the credentials
    az aks get-credentials \
    --resource-group kbgroup2 \
    --name kbcluster2 

    kubectl get nodes


Save the file deployment.yaml with the following contents.

    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: redis
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      strategy:
        rollingUpdate:
          maxSurge: 1
          maxUnavailable: 1
      minReadySeconds: 5 
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: microsoft/azure-vote-front:v1
            ports:
            - containerPort: 80
            resources:
              requests:
                cpu: 250m
              limits:
                cpu: 500m
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    

####Deploy the application
    // Get a list of cluster nodes
    kubectl get nodes

    // Run the application
    kubectl apply –f deployment.yaml

####Monitor progress of the deployment
    // Monitor progress of the deployment
    kubectl get service azure-vote-front --watch

    // View pods related to this deployment
    kubectl get pods -l app=azure-vote-front

####Demo 2 (Create an ACR account though Azure CLI)

    // Create an ACR instance
    az acr create \
     --resource-group kbgroup2 \
     --name acrdemo002 \
     --sku Basic

    // Login to ACR
    az acr login --name acrdemo002

####Build Image to publish to ACR

    // Pull existing Docker image
    docker pull microsoft/aci-helloworld

    // Obtain the full login server name of the ACR instance
    az acr list --resource-group kbgroup2  --query "[].{acrLoginServer:loginServer}" --output table

    // Tag image with full login server name prefix
    docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
  
    // login into acr
    az acr login  

    // Push image to ACR
    docker push <acrLoginServer>/aci-helloworld:v1

####View deployment images

    // List container images
    az acr repository list --name acrdemo002 --output table

    // List the tags on the aci-helloworld repository
    az acr repository show-tags --name acrdemo002 --repository aci-helloworld --output table

####Deploy an image to ACR by using Azure CLI

    // Enable admin user
    az acr update --name acrdemo002 --admin-enabled true

    // Query for the password
    az acr credential show --name acrdemo002 --query "passwords[0].value"

    // Deploy container image
    az container create --resource-group kbgroup2  --name acr-quickstart --image <acrLoginServer>/aci-helloworld:v1 --cpu 1 --memory 1 --registry-username acrdemo002 --registry-password <acrPassword> --dns-name-label acrdemo002 --ports 80

    // View container FQDN
    az container show --resource-group kbgroup2  --name acr-quickstart --query instanceView.state

###Demo 3 - Create Container from Dockerfile

Run

    git clone https://github.com/Azure-Samples/aci-helloworld.git

Build the image

    // Build your container
    docker build ./aci-helloworld -t aci-tutorial-app

    //confirm the builded image
    docker images
    
    //something like
    //aci-tutorial-app latest 5c745774dfa9 39 seconds ago 68.1 MB

    // Run your container locally
    docker run -d -p 8080:80 aci-tutorial-app

    // View running containers
    docker container ls -a
   
    //tag as V2
    docker tag aci-tutorial-app:latest acrdemo002.azurecr.io/aci-tutorial-app:v2

    //push image to container registr
    docker push acrdemo002.azurecr.io/aci-tutorial-app:v2

####Deploy to container registry (ACI)

    // Get name of container registry login server
    az acr show --name acrdemo002 --query loginServer

    // Get container registry password
    az acr credential show --name acrdemo002 --query "passwords[0].value"

    // Deploy container
    //<acrLoginServer> and <acrPassword> with the values that you obtained from cmds. 
    //<acrName> the name of your container registry.
    //<aciDnsLabel> with desired DNS name
    az container create --resource-group kbgroup2 --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v2 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <acrName> --registry-password <acrPassword> --dns-name-label <aciDnsLabel> --ports 80

###Demo 4 - Implementing an application using Virtual Kublet

    // Install Virtual Kubelete connector
    az aks install-connector --resource-group kbgroup2 --name kbcluster2 --connector-name virtual-kubelet --os-type Both

Create a virtual-kubelet.yaml

    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: aci-helloworld
    spec:
      replicas: 1
      template:
        metadata:
          labels:
            app: aci-helloworld
        spec:
          containers:
          - name: aci-helloworld
            image: microsoft/aci-helloworld
            ports:
            - containerPort: 80
          nodeSelector:
            kubernetes.io/hostname: virtual-kubelet-virtual-kubelet-linux
          tolerations:
          - key: azure.com/aci
            effect: NoSchedule
    
Deploy it

    // Validate that Virtual Kubelet has been installed
    kubectl get nodes

    // Run application (Windos or Linux)
    kubectl create -f virtual-kubelet.yaml

    // View pods with the scheduled node
    kubectl get pods -o wide


Refs.:
>https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough-portal#run-the-application

>https://docs.microsoft.com/en-us/azure/aks/kubernetes-walkthrough

