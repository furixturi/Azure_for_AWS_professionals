# Azure for AWS Professionals

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/

## TOC

- [Account and subscriptions](#Account and subscriptions)
- [Compute Services](#Compute Services)
- [Database and data analytics](#Database and data analytics)
- [Storage](Storage)
- [Messaging and Event](#Messaging and Event)
- [Networking](#Networking)
- [Regions and zones](#Regions and zones)
- [Resource management](#Resource management)
- [Security and identity](#Security and identity)



## Account and subscriptions

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/accounts

- Azure subscriptions are **grouping of resources** with **an assigned owner** responsible for billing and permissions management.
- Azure subscriptions are **independent** of owner accounts, and can be reassigned to new owners as needed.
- Classic subscription administrator roles:
  - Account administrator:
    - subscription and billing owner
    - Only one per subscription.
  - Service Admnistrator: 
    - can create and manage resources but not responsible for billing
    - for a new subscription, the account admnistrator is also the service administrator
      - account administrator can assign a separate user to the service administrator
    - Only one per subscription
  - Co-administrator
    - same access privileges as the service admnistrator
    - multiple per subscription
- Azure RBAC (role based access control)

## Compute Services

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/compute

### Virtual Machines

- Amazon EC2 - Azure Virtual Machines
- VMware Cloud on AWS - Azure VMware Solution
  - VMware vSphere-based workload
- AWS Parallel Cluster - Azure CycleCloud
  - HPC clusters

### Autoscaling

- AWS: Auto Scaling 
- Azure: Virtual Machine Scale Sets 
  - Layer-4 traffic distribution with **Azure Load Balancer**
  - Layer-7 traffic distribution and TLS termination with **Azure Application Gateway**
  - Orchestration modes
    - uniform
      - identical VM instances
      - HA guaranteed for <= 100 instances
      - supports metrics-based autoscaling, instance protection, automatic OS upgrades
    - flexible
      - HA guaranteed for <= 1000 VMs
      - spread VM across fault domains in a region or within an AZ
      - fault domain isolation, essential to quorum-based or stateful workloads
        - Quorum-based workloads
        - OS databases
        - Stateful applications
        - mix virtual machine types or Spot and on-demand VMs
        - supports memory preserving update
- Azure: App Service autoscale

### Batch Processing

- AWS Batch - Azure Batch
  - no infra (VM) management, no batch scheduler install
  - for large-scale parallel and HPC batch jobs
    - financial risk modeling
    - VFX and 3D image rendering
    - image analysis and processing
    - media transcoding
    - genetic sequence analysis
    - OCR
    - data ingestion, processing, ETL
    - software test execution
  - use batch with MPI (message passing interface) API to run tightly coupled workloads that need to communicate with each other

### Storage

- Amazon EBS - **Azure managed disks**
  - Disk types:
    - Ultra disk: SSD 
      - for IO-intensive, transaction-heavy workload (SAP HANA), top tier DB (SQL, Oracle)
      - max size: 65 TB, throughput 10,000 MB/s, IOPs 400,000
    - Premium SSD v2 - best price performance
      - performance intensive workload, low latency high IOPS and throughput
      - max size: 65 TB, throughput 1,200 MB/s, IOPS 80,000
    - Premium SSD
      - production and performance sensitive workloads
      - max size 32TB, throughput 900MB/s, IOPS 20,000
      - can be OS disk
    - Standard SSD
      - web server, dev/test application
      - max size 32TB, throughput 750MB/s, IOPS 6000
      - can be OS disk 
    - Standard HDD
      - backup, non-critical, infrequent access workload
      - max size 32TB, throughput 500MB/s, IOPS 2000
      - can be OS disk
  - Disk roles: 
    - Data disk
    - OS disk: has a pre-installed OS, contains the boot volume
    - Temporary disk: short-term storage intended for data such as page files, swap files, SQL Server tempdb
  - Image vs snapshot
    - image: all attached disks to a VM, can be used to create a VM
    - snapshot: only the one disk at the point in time when the snapshot is taken

- Amazon EC2 instance store - **Azure managed disk - temporary disk** role
- Amazon EBS provisioned IOPS volume - **Azure managed disk - premium SSD v2 **
- Amazon EFS: Azure Files
  - supports SMB, NFS protocol and Azure Files REST API

### Container

- Amazon ECS/EKS Fargate - **Azure Container Apps**
  - fully managed, no k8s API access
  - price per vCPU, memory second
  - event-driven or automatic scale 
  - can scale to zero
- Amazon ECR - **Azure Container Registry**
- Amazon EKS - **Azure Kubernetes Service (AKS)**
  - partially managed, provides k8s API access
  - price per node hour
  - manual scale possible
  - can only scale to zero by using KEDA
- AWS App Mesh - **Open Service Mesh (OSM) add-on** in AKS
  - service observability
  - runs an Envoy-based control plane on k8s
  - inject and Envoy proxy as a sidecar container with each AKS instance
  - Envoy proxy contains and executes access control rules and policies, implements routing config, captures metrics

### Serverless

- AWS Lambda - **Azure Functions**
  - Hosting plans
    - Consumption: 
      - timeout: default 5min, max 10min
      - default. auto-scale but cold start when adding instances
    - Premium plan
      - timeout: default 30min, max unlimited (guaranteed 60min)
      - pre-warmed workers
      - more CPU and memory (powerful instances)
      - for long-running code, continuously running apps
      - virtual network connectivity
    - Dedicated (Azure app service) plan
      - timeout: default 30min, max unlimited (guaranteed 60min)
      - predictive scaling and costs
    - Azure Container Apps (preview)
  - HTTP timeout: 230 sec - default idle timeout of Azure Load Balancer
  - Durable Functions
    - define stateful workflows by writing **orchestrator functions**
    - manage, manipulate state entities by writing **entity functions**
- **WebJobs in Azure App Service**
  - for running background tasks
  - continuous, or triggered manually or on schedule

## Database and data analytics

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/databases

### Transactional relational database

Amazon RDS/Aurora -

- **Azure SQL Database**
  - fully managed, serverless, near-limitless storage
- Open source:
  - **Azure Database for MySQL, PostgreSQL, MariaDB**
- Hosted on VM
  - SQL Server, Oracle, MySQL deployed on Azure VM

### NoSQL

- Amazon DynamoDB/Neptune/DocumentDB - **Azure Cosmos DB**
  - globally distributed cloud DB service
  - fully managed NoSQL and relational DB
  - supports multiple DB APIs:
    - PostgreSQL
      - powered by Citus open source extension of PostgreSQL (distributed PostgreSQL)
    - MongoDB
      - implement the wire protocol for MongoDB
      - not running or affiliated with MongoDB
      - choice between vCore-based and RU-based
        - **vCore**
          - lift and shift an existing MongoDB workload
          - more long-running queries, complex aggregation pipelines, distributed transactions, joins
          - high-capacity vertical and horizontal scaling with vCore-based cluster tiers (M30, M40, M50)
          - **native support for storing and searching vector embeddings**
          - multi-document ACID transactions
        - RU
          - cloud-native MongoDB app
          - more point reads, few long-running queries and complex aggregation pipeline operations
          - limitless horizontal scalability, instantaneous scale up, granular throughput control
          - mission-critical, 99.999% availability
    - Apache Cassandra
      - RU
    - Apache Gremlin
      - graph database service API for Gremlin
      - based on Apache TinkerPop
        - some implementation details are different. some TinkerPop features are not available
      - automatic indexing
      - tunable consistency levels
        - strong, bounded-staleness, session, consistent prefix, eventual
    - Table
      - for Azure Table storage with premium capabilities
        - turnkey global distribution
        - dedicated throughput worldwide (when provisioned)
        - single-digit millisecond latencies at the 99th percentile
        - automatic secondary indexing
  - normalizes all DB operation resource costs (CPU, IOPS, memory) to **Request Unit (RU)s**, measures cost based on throughput (RU/s)
    - 1 point read (fetch a single item by ID and partition key) of 1KB: 1RU
  - **Azure Synapse Link** for Azure Cosmos DB
    - hybrid transactional and analytical processing (HTAP) capability
    - near real-time analytics over operational data in Azure Cosmos DB
    - No effect on operational workload
    - cost effective
    - analytics for locally available, globallly distributed, multi-region writes
    - native integration with **Azure Synapse Analytics**
  - Azure AI Advantage offer
    - for existing Azure AI and GitHub Copilot customers
      - free 40,000 RU/s throughput for 90 days
      - funding to new AI application using CosmosDB and/or Azure Kubernetes Service

- Amazon Keyspaces - **Azure Managed Instance for Apache Cassandra**
  - fullly managed service for pure open source Aoache Cassandra clusters
  - can scale out of existing on-premises or cloud self-hosted Apache Cassandra clusters by adding managed Cassandra datacenters to the existing cluster ring
- Azure Table
  - only pay for capacity that's used (unlike Cosmos for Table)

- Amazon ElastiCache / MemoryDB for Redis - Azure Cache for Redis

### Database migration

- AWS DMS (+SCT) - Azure Database Migration Service

### Analytics and big data

- Amazon EMR (Managed Apache Hadoop) - **Azure HDInsight**
  - managed Apache Hadoop, can run Spark, Hive, Kafka, HBase
- AWS Glue - **Azure Data Factory**
  - cloud serverless ETL service, build data pipelines without infra management
  - offers a code-free UI

- Amazon Redshift - **Azure Synapse Analytics**

  - distributed query system for T-SQL
  - built-in streaming
  - seamlessly integrates Apache Spark
  - Data Explorer (preview) for near real time querying log and telemetry data

- **Azure Databricks**

- Data Lake - **Azure Data Lake Storage Gen2**

  - built on Azure Blob Storage

- Amazon Kinesis - **Azure Stream Analytics**

- Amazon QuickSight - **Power BI**

  

## Storage

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/storage

In Azure, subscription-bound **storage account**s let you manage your storage services:

- Amazon S3 - **Azure Blob Storage**
  - Glacier - **Azure Archive Blobe Storage** tier
  - IA - **Azure Cool Blob Storage** tier
- Amazon EBS - **Azure managed disk**
- Amazon EFS - **Azure Files**

- **Azure Table storage** - key attribute data store like DynamoDB
- **Queue storage** - store large numbers of messages

### Backup service

- AWS Backup - **Azure Backup**
  - durable options
    - LRS: locallly redundant storage (3 replica within the storage account region)
    - ZRS: zone-redundant storage (3 replica across two to three facilities, within 1 or 2 region)
    - GRS: geo-redundant storage (6 replica. replicate to a secondary region)
      - RA-GRS: replicate to a secondary region, but read-only in the secondary region

### Hybrid

- AWS Storage Gateway - **StorSimple** (retired) -> **Azure File Sync**
- AWS DataSync (interval >= 1h) - **Azure File Sync**

- AWS Snowball - **Azure Data Box**
  - **Azure Data Box Gateway**: virtual device on-premises. helps manage large-scale data migration
  - Import/Export jobs
- AWS Outpost - **Azure Stack Edge**



## Messaging and Event

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/messaging

- Amazon SES/SNS - **Azure Communication Services**
  - email, video, voice, chat, SMS, mobile
- Amazon SQS - **Azure Queue Storage**, **Azure Service Bus**
  - **Azure Relay**

- Amazon SNS/Amazon MQ - **Azure Service Bus**
  - Service Bus Premium is fully compliant with the Java/Jakarta EE Java Message Service (JMS) 2.0 API. Service Bus Standard supports JMS 1.1 subset focused on queues
  - advanced messaging features: FIFO, batching/sessions, transactions, dead-lettering, routing and filtering, duplicate detection
  - use cases: order processing, financial transaction
- Amazon EventBridge - **Azure Event Grid**
  - discrete event distribution, uniform event routing and consumption using pub/sub model
  - purpose: reactive programming
  - use case: react to status change
- Amazon Kinesis - **Azure Event Hub**
  - fully managed, real-time data ingestion service, series of event streaming
  - can receive and process millions of events per second
  - purpose: big data pipeline
  - use case: telemetry and distributed data streaming



## Networking

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/networking

### Load balancing

- Amazon Load Balancing NLB - **Azure Load Balancer**
  - OSI model Layer 4 load balancing
  - public and internal (private) load balancers
  - use health probes to monitor
  - port forwarding
  - IPv6 support
- Amazon ALB - **Azure Application Gateway**
  - OSI model Layer 7 load balancing
  - Layer 7 DDoS protection using WAF 
    - for L3 L4 DDoS protection, use **Azure DDoS Protection** plan

### DNS

- Amazon Route 53 DNS service - **Azure DNS**
  - domain and DNS management
  - based on Azure Resource Manager, which provides
    - Azure RBAC, activity logs, resource locking
  - **does not support DNSSEC**
- Amazon Route 53 DNS routing - **Azure Traffic Manager**
  - DNS level traffic routing, load balancing, failover
  - 6 traffic routing options:
    - priority, performance, geographic, weighted round-robin, subnet, multi-value
  - on-premises scenarios:
    - burst-to-cloud, migrate-to-cloud, failover-to-cloud
  - geo-fencing: for content localization, data sovereignty regulation

### Site-to-site connection

- Amazon Direct Connect - **Azure ExpressRoute**
- AWS VPN - **Azure VPN Gateway**, **site-to-site VPN**

### VPC

- Amazon VPC - Azure **Virtual Network**
  - AWS custom route table - **user-defined routes**
- AWS VPC Peering - **VNet peering**
- AWS NAT gateway - **Virtual Network NAT**
- AWS VPC endpoints - **Private Endpoint**
  - deploy to a subnet and a private IP address is assigned
  - the service to connect:
    - Azure Storage
    - Azure Cosmos DB
    - Azure SQL Database
    - your own service using Private Link
- AWS PrivateLink - **Azure Private Link**
- AWS VPC Flow Logs - **Azure Network Watcher**

### CDN

- Amazon CloudFront - **Azure Front Door**
  - global service, deployed to all edge locations
  - attached WAF, DDoS protection, bot protection
  - dynamic and static content acceleration
  - global loda balancing
  - traffic and security analytics



## Regions and zones

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/regions-zones

### Scope of failure

- Multi **Availability Set**s
  - Scope of failure: Rack (VMs that share a power source and network switch)
  - Routing: Load Balancer
  - Latency: very low
  - Networking: VNet
- Multi **Availability Zone**s
  - Scope of failure: Datacenter (separated groups of datacenters within the same region)
  - Routing: Cross-zone Load Balancer
  - Latency: Low
  - Networking: VNet
- Multi-**Region**
  - Scope of failure: Region
  - Routing: Traffic Manager
  - Latency: mid to high
  - Networking: cross-region VNet peering
  - **region pair**
    - each Azure region is paired with another region within the same geography



## Resource management

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/resources

- AWS CloudFormation - **ARM Template**
  - ARM: Azure Resource Manager
  - JSON template
  - Bicep language or powershell script to deploy and manage resources
- Terraform
- Management interfaces
  - Web
  - REST API
  - command line
  - PowerShell: script
  - Templates
- resource group:
  - a resource can only be tagged to one resource group, unlike AWS



## Security and identity

https://learn.microsoft.com/en-us/azure/architecture/aws-professional/security-identity

### Multicloud identity

- **Microsoft Entra ID** and SSO
  - use Microsoft Entra ID as a federation and authentication source for AWS IAM
  - consolidate identity service between Azure and AWS
  - role-based access control across AWS and Azure
    - using rules to associate`user.userprincipalname` and `user.assignrole` attributes from Micorsoft Entra ID into IAM permissions
- **Privileged Identity Management (PIM)**
  - provide just-in-time access to Azure services
  - control and limit access using assignment model for roles
  - eliminate persistent access for prvileged accounts

### Cloud security

- **Microsoft Defender for Cloud** (multi-cloud security)
  - a cloud -native application protection platform (CNAPP)
  - provides unified security management and advanced threat protection across hybrid cloud workloads (Azure, AWS, GCP)
    - DevSecOps solution: code level security management
    - CSPM (cloud security posture management) solution: surfaces actions to prevent breach
    - CWPP (cloud workload protection platform): specific protections for servers, containers, storage, databases, etc.
- **Microsoft Sentinel**
  - scalabel, cloud-native solution that provides 
    - SIEM (security information and event management)
    - SOAR (security orchestration, automation, response)
  - attack detection, threat visibility, proactive hunting, threat response