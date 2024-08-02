+++
categories = ["AZ-203", "en", "Azure"]
date = 2020-01-03T21:25:54Z
description = ""
draft = false
image = "https://images.unsplash.com/photo-1597138768744-9f97be8cdd64?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3wxMTc3M3wwfDF8c2VhcmNofDN8fGRhdGFjZW50ZXJ8ZW58MHx8fHwxNjg0MTIyMTI0fDA&ixlib=rb-4.0.3&q=80&w=2000"
slug = "azure-storage-disks"
tags = ["AZ-203", "en", "Azure"]
title = "Azure Storage Disks - Managed and unmanaged disks"

+++


##Managed disks
- The Azure platform manages the disk and the backing storage
- You don't have to worry about storage account limits and thresholds

##Unmanaged disks
- You manually create and manage virtual hard disks (VHDs) in your Storage account
- You will need to consider account throughput and capacity limits when using this model


**Managed disks** - Managed disks are the newer and recommended disk storage model. They elegantly solve this complexity by putting the burden of managing the storage accounts onto Azure. You specify the size of the disk, which can be up to 4 terabytes (TB), and Azure creates and manages both the disk and the storage. You don't have to worry about storage account limits, which makes managed disks easier to scale out than managed discs.

**Unmanaged disks** - With unmanaged disks, you’re responsible for the storage accounts that hold the virtual hard disks (VHDs) that correspond to your VM disks. You pay the storage account rates for the amount of space you use. A single storage account has a fixed-rate limit of 20,000 input/output (I/O) operations per second. This means that a storage account is capable of supporting 40 standard VHDs at full utilization. If you need to scale out with more disks, then you'll need more storage accounts, which can get complicated.

## Disk Types in Azure

### Ultra disk
Azure ultra disks deliver high throughput, high IOPS, and consistent low latency disk storage for Azure IaaS VMs.

### Premium SSD
Azure premium SSDs deliver high-performance and low-latency disk support for virtual machines (VMs) with input/output (IO)-intensive workloads.

### Standard SSD
Azure standard SSDs are a cost-effective storage option optimized for workloads that need consistent performance at lower IOPS levels.

### Standard HDD
Azure standard HDDs deliver reliable, low-cost disk support for VMs running latency-insensitive workloads.

Checkout the official [disk comparison](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#disk-comparison)



