+++
categories = ["Azure", "az-cli", "Azure-Batch", "AZ-203", "en"]
date = 2019-06-02T21:25:27Z
description = ""
draft = false
image = "https://res-4.cloudinary.com/dgoa5llvh/image/upload/q_auto/demo-batch-cover.jpg"
slug = "demo-azure-batch"
tags = ["Azure", "az-cli", "Azure-Batch", "AZ-203", "en"]
title = "Demo - Azure Batch"

+++


####Creating the Resource Group
     
    az group create --name "az203_batch_demo001" --location CentralUS
    
####Creating the storage account
 
    az storage account create \
      --name az203batchstoragexd \
      --access-tier hot \
      --https-only true \
      --kind BlobStorage \
      --location CentralUS \
      --sku Standard_LRS \
      --resource-group az203_batch_demo001
     
####Creating the Batch Account
    az batch account create \
      --name az203batchaccountxd \
      --storage-account az203batchstoragexd \
      --resource-group az203_batch_demo001 \
      --location CentralUS
     
####Login and create the Pool
    az batch account login \
      --name az203batchaccountxd \
      --resource-group az203_batch_demo001 \
      --shared-key-auth
     
    az batch pool create \
      --id mypool \
      --vm-size Standard_A1_v2 \
      --target-dedicated-nodes 2 \
      --image canonical:ubuntuserver:16.04-LTS \
      --node-agent-sku-id "batch.node.ubuntu 16.04"
     
    az batch pool show \
      --pool-id mypool \
      --query "allocationState"

####Creating the Job inside the Pool
     
    az batch job create --id myjob --pool-id mypool

######Job Task (Execution commands)
     
    for i in {1..4}
    do
       az batch task create \
        --task-id mytask$i \
        --job-id myjob \
        --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
    done
     
    az batch task show \
         --job-id myjob \
         --task-id mytask1
     
     	
    az batch task file list \
         --job-id myjob \
         --task-id mytask1 \
         --output table
     
    az batch task file download \
         --job-id myjob \
         --task-id mytask1 \
         --file-path stdout.txt \
         --destination ./stdout.txt
     
###After done experimenting
Don't forget to delete the resources created.

    az group delete --name az203_batch_demo001 --yes

Refs.:
>https://docs.microsoft.com/en-us/azure/batch/quick-create-cli

