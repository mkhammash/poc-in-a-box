# poc-in-a-box

## Overview
This is an application designed for Prisma Network Security Module demo's and POV's.

## Details

### Prerequisite's
* A 'Prisma Cloud' account and corresponding Tenant ID
* A public cloud account (AWS, AZURE, GCP) or private cloud
* A Kubernetes cluster (EKS, AKS, GKE, OC4) and corresponding kubectl and config/credentials
* A Linux or Mac workstation

You will need these variables. You can obtain them from your Prisma Account or your SE.
| Variable | Value                |
| -------- | -------------------- |
| $TENANT  |                      |
| $CLOUD   |                      |
| $GROUP   |                      |

### Directories
The directory bin holds scripts and executables that will be used as part of the POV. The directory 'all' holds convenience scripts that will be documented later. The directories 'cloud', 'group', 'knoxville', 'memphis' and 'nashville' align with Prisma namespaces. The mapping is as follows:

| Directory | Prisma Namespace | Tag | Alias |
| cloud | /$TENANT/$CLOUD | @org:cloudaccount ||
| group | /$TENANT/$CLOUD/$GROUP | @org:tenant | K8S Cluster |
| nashville | /$TENANT/$CLOUD/$GROUP/nashville | @org:kubernetes | K8S Namespace |
| memphis | /$TENANT/$CLOUD/$GROUP/memphis | @org:kubernetes | K8S Namespace |
| knoxville | /$TENANT/$CLOUD/$GROUP/knoxville | @org:kubernetes | K8S Namespace |

Each directory contains one or more of the following:
* Kubernetes config in YAML format
* Prisma Network Security config in YAML format
* Scripts written in bash

The directories cloud and group hold Prisma Network Security config that will be applied to the cloud account and group (Kubernetes cluster). The directories Nashville, Memphis and Knoxville are each applications. Nashville and Memphis are virtually identical with the exception of their name. They contain a frontend and backend pod type. The backends form a cluster and the frontends talk to all of the backends. Frontends do not talk to each other. The backends for both also talk to the Nashville app database pod type. The Nashville app has only a single app type; the aforementioned database. The database pods create a cluster inside the Nashville app and accept incoming connections from Memphis and Knoxville backend pod types.

### Scripts
The directories cloud, group, knoxville, memphis and nashville each contain a script named 'create' and 'delete'. The 'create' script is used to apply Kubernetes and/or Prisma configuration and the 'delete' script is used to remove said configuration. The directories memphis and knoxville also include a script called 'attack' that is used to cause the app to make unauthorized connections.

The directory 'all' also holds a script named 'create' and 'delete'. These are convenience scripts that will call the create or delete script in each of the aforementioned directories in the correct order. 

The order of 'create' is
1. Cloud
1. Group
1. App (nashville, memphis, knoxville)

The order of 'delete' is
1. App (nashville, memphis, knoxville)
1. Group
1. Cloud

## Policy

Policy can be grouped into infrastructure, company policy and application. 

### Infrastructure
This is common services such as DNS and NTP. These are created once and then inherited. These may exist at different levels within the namespace. For example all workloads running a given cloud account may be authroized to talk the the local DNS server and workloads running in a given Kubernetes cluster may be able to talk to the local Kube DNS. Because the workloads are children of both the Kubernetes cluster and the Cloud they may talk to both DNS servers. But workloads in different Kubernetes clusters may not talk to Kube DNS running in other clusters. The same thing applies to workloads running in different cloud providers.

## Company Policy
These are policies based on company (or organization) policies. These are inherited and by children and can NOT be overridden. These are policies, for example, such as production may not talk to development. These are usually governed by the SecOps team. The SecOps team is advised to be careful with these policies as it requires the involvment of the SecOps team to modify these policies.

### Application
These are policies that govern the behaviour of an application for both intra-application and inter-application communication. These are usually under the control of the application team and stored in the CICD pipleline. When workloads running in the App need to communicate the App team may create policy to authorize these flows. The security control is the CICD pipeline (code reviews, etc). For inter-app communication both App teams must create a policy to permit the flow. Once again the security control is the CICD pipeline.

## Instructions

### Quick

1. Clone this repo
1. Change directory into the repo
1. Edit the file 'settings' and update TENANT, CLOUD, and GROUP
1. Source settings into your shell environment
1. Configure your Prisma App Creds
1. Run the script all/create
1. Once you are finished run the script all/delete

### Example
```bash
git clone https://github.com/aporeto-se/poc-in-a-box.git
cd poc-in-a-box
# edit the file settings
source env_shell
apoctl configure -n /$GROUP/$CLOUD -t $TOKEN
all/create
```

Whe finished
```bash
all/delete
```

## POV
Before starting the POV you should have poc-in-a-box up and running. We will be working from the Prisma Cloud console unless otherwise noted. We will also assume that you are working from "Network Security" and the namespace level is group (your Kubernetes cluster)

### Visibility

1. Go to AppDependencyMap
1. Note the solid green flows between workloads (Hexagon Icons)
..* in the same namespace
..* in different namespaces
1. Note the flows to/from networks (Square Icon) such as MetaDataAPI, Google, KubeDNS, etc
1. Click on a flow and take note of the policy
1. Go to Rulesets


