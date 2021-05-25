# poc-in-a-box

## Overview
This is an application designed for Prisma Network Security Module demo's and POV's.

## Directories
The directories 'cloud', 'group', 'knoxville', 'memphis' and 'nashville' hold Prisma and/or Kubernetes configuration. They also each contain a script named 'create' and 'delete'. These scripts are used to create and delete policy. The directory 'all' contains the scripts 'create' and 'delete' only. These scripts can be used to call the other 'create' and 'delete' scripts in the correct order. If the scripts in all are not used it is important that the 'create' and 'delete' scripts be called in the correct order. For 'create' the order is 'cloud', 'group', and then 'knoxville', 'memphis' and 'nashville' in any order. For 'delete', 'knoxville', 'memphis' and 'nashville' must be deleted first in any order and then 'group' then 'cloud'. 

### Cloud
This directory holds Prisma configuration and it aligns with the customer 'Cloud Account' namespace which is a child of Tenant. The configuration will take care of configuring tag prefixes and creating account level policy and networks.

### Group
This directory holds Prisma configuration and it aligns with the customer Kubernetes cluster namespace which is a child of the 'Cloud Account'. The create script will automatically resolve Kubernetes level information and create policies such as Kube DNS. The create script should be called after the cloud create script. The delete script should be called before the cloud delete script.

### Nashville
This directory holds Prisma and Kubernetes configuration for an application of the same name. It is a child of the 'group' and also a Kubernetes namespace. This application is composed of a "databases" that all communicate with each other. The Prisma policy permits these flows. The Prisma policy also permits flows from Knoxville and Memphis "backends".

### Knoxville & Memphis
These directores are virtually identical with the exception of the name. They are both composed of a "frontend" and "backend". The "frontend" communicates with the "backends" and "backends" communicate with each other. For clarification "frontends" should not talk to other "frontends". The "backend" also communicates with the "database" in Nashville ("database" is the server).

## Simulated Cloud, Region & Account
The tags 'sim-cloud', 'sim-region' and 'sim-account' are used for simulation purposes. This allows us to simulate multiple clouds, regions and accounts within our single cluster. To accomplish this each deployment.yaml is composed of multiple deployments with these aforementioned tags. For example a podset named frontend will actually be named frontend1, frontend2, frontend...N.

## Policy

Policy can be grouped into infrastructure, company policy and application. 

### Infrastructure
This is common services such as DNS and NTP. These are created once and then inherited. These may exist at different levels within the namespace. For example all workloads running a given cloud account may be authroized to talk the the local DNS server and workloads running in a given Kubernetes cluster may be able to talk to the local Kube DNS. Because the workloads are children of both the Kubernetes cluster and the Cloud they may talk to both DNS servers. But workloads in different Kubernetes clusters may not talk to Kube DNS running in other clusters. The same thing applies to workloads running in different cloud providers.

## Company Policy
These are policies based on company (or organization) policies. These are inherited and by children and can NOT be overridden. These are policies, for example, such as production may not talk to development. These are usually governed by the SecOps team. The SecOps team is advised to be careful with these policies as it requires the involvment of the SecOps team to modify these policies.

### Application
These are policies that govern the behaviour of an application for both intra-application and inter-application communication. These are usually under the control of the application team and stored in the CICD pipleline. When workloads running in the App need to communicate the App team may create policy to authorize these flows. The security control is the CICD pipeline (code reviews, etc). For inter-app communication both App teams must create a policy to permit the flow. Once again the security control is the CICD pipeline.

1. Deploy this
1. View the "App Dependency Map"
1. Look at the policies
