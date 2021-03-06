# AWS Learning

AZ - AVailabitly zone

## S3 

max file size is 5T

* S3 standard
* S3 IA (infrequently accessed)
* S3 IA one zone 
* S3 Intelligent Tiering
* S3 Glacier (min to hours)
* S3 Glacier deep archive (12 hours)

Read after write consistency
eventual consistency for overwrite puts and deletes

99.99 availability
99.99... (11X9) durability ( unlikely to be lost) 

###Features

Tiered storage
Life cycle management
versioning
encytpion
mfa delete
access control list 

### Encryption

In transit : https/SSL/TSL

At rest : 
- client side : Own keys 
- server Side : 
   S3 managed keys SSE-S3 (ServerSide Encryption S3)
   Key management Service - SSE-KMS
   Customer Provided Keys (SSE-C) 


Once the versioning is enabled it can't be disabled only suspended
Uploading the same file with same name will not preserve the file permissions

Cross region replication requires versioning to be enabled
Replication starts from the point it was created . Any existing objects will not be replicated

MFA for delete

Transfer acceleration. Uses cloudFront instead uploading directly to the s3 bucket

Cloudfront runs on the edge location and enables the content transfer much faster

Cloudfront - invalidation rules is needed to remove all the contents 

Snoball - 50Tb to 80TB 
comes with encryption
Snowball edge -- compute and storage 
  - 100TB 
Snowmobile - exabyte transfer 100 PB 

### Storage Gateway


* NFS (s3)
* ISCSI (EBS)
  Stored voumes - mounted locally and backed up to EBS snapshot periodically 
  cache volumes - It is the primary storage in AWS 
  taped volumes - vtl backup taken directly to the AWS 


READ THE FAQ OF S3 BEFORE THE EXAM


# EC2

* On Demand
* Reserved
* Spot 
* Dedicated hosts 


Security groups
* Stateful - Any change to the outbound rule will not impact the inbount rune

NACL - stateless (need to create inbound/outbound rules explicitly)

* Cann't block any ip address for any port opening

* All inbound traffics are blocked by default 

*  All outbound traffics are allowed by default 

* instance can have multiple security group


## EBS

* General purpose ssd ( For general purpose) gp2 16000 ops - gp2
* Provisioned iops ssd ( For databases)  -io1 64000 ops - io1
* Throughput optimized (HDD) 500 ops ( data warehouse)- st1
* Cold HDD. - 250 ops ( low cose ) (For file servers) - sc1
* Standard (HDD) 40-200 ops 


EBS volumes should be in the same AZ as the EC2 instance

How to move the volume across the AZ
- Create a snapshot 
- Create an image
- Use the image to provision EC2

Snapshot exits on the S3
Snapshot is incremental 

Its better to stop the instance before taking the snapshot of the root volume 

EBS volume type and size can be changed on the fly

To move the volume across the regions
- Create a snapshot
- Create an image out of the snapshot
- Copy the image to different region
- Use the image to provision EC2


PV or HVM virtualization
HVM is the default and provides many options 

EBS Vs instance store

instance store is ephemeral and can't be stopped
Instance store is very limited interms of choosing the type 


## Encrypting the EBS volume

- Create a snapshot
- Copy the snapshot and encrypt it
- Create image from the snapshot
- Use the image to provision the server 

EC2 placement options

Cluster placement group
Spread placement group
Partition placement group 

CloudWatch - monitoring performance (every 5 min by default or 1 min if advanced enabled)
CloudTrail - For auditing 

Memory is not monitored by the cloudwatch 


# Databases

Max of 5 read replicas in different AZ

RDBMS
Oracle/SQL Server/MariaDB/MySQL/PostgreSQL/Aurora

NOSQL
 DynamoDB

Scaleout configuration
Redshift (OLAP)

Elasticcache
 memcached
 redis

Multi-AZ is used only for the disaster recovery 
The read replicas (async) is used only for performance
The read replicas can be promoted but only as a standalone database 

Each read replicas will have its end point 
Read replicas can be in a second region 

Backups needs to be turned on for the read replicas to be enabled

Backup retention is up to 35 days 

Aurora is does not have multi-az or read replicas , since it uses different mechanism


Dynamodb 
  1 sec rule for eventual consistency and Strongly consistent reads

Redshift
  Single node
  Multi-node (leader and worker) 
   
  Columnar compression
  Available only in one AZ 


Aurora 

   5X than mysql
   3X than postgresql

 10Gb - 64Tb
 2 copies of data  in 3 AZ (6 copies of data) 
 
 Loss of 2 copies for read availability
 Loss of 3 copies for write availability

  Aurora replicas (15)

  MySQL can have read replicas with Aurora
  The read replicas can be promoted to convert the mysql to aurora cluster 


# Route53


NS = name server

SOA - Start of authority records
NS - Name server
PTR records - Reverse of A records

A - Record Address (Final resolution of the domain name with IP address)
TTL - Time to Live in seconds (default 48 hours)

CName - Canonical Name : To map one domain with another domain name 
Alias records : Same as CNAME 

Cname can't be used for naked domain name (zone apex record) (http://myraj.com) 

ELB will not have ipv4 address.

## Routing techniques


* Simple routing - Picks the IP address in random order - No health check
* Weighted routing - Provides weight to each ip address allocation 
* Latency-based routing - Route to lowes network latency 
* Failover routing   - Active/Passive based on the health check monitoring
* Geolocation routing - Routes based on the location of the request initiation 
* Geoproximity rouing - Available only in the traffic flow only . Routes based on the geolocation of request and resources along with bias
* Multivalue routing - Same as simple routing with additional healthcheck for each records 

Healthcheck - Healthchecks can be created to monitor the application and route53 will disable if the health check fails


# VPC


Network ACL - stateless (allow and deny, based on the order)
SG - Stateful (only allow, no specific order)

VPC creates the following by default
   Security Group
   Route table
   Network ACL
   
subnet can't spawn across AZ
VPC can't spawn Region

Only one IG can be attached to VPC

Subnect can be attached to only one ACL at a time

Default ACL by default allows all the communication
Custom ACL by default denies all the communication 

## Load balancers

- Application LB
- Network LB
- Classic LB

Atleast two subnet is required to create the load balancers


## VPC flow logs

To capture information flowing between network interfaces in the VPC . Stored in cloudwatch logs and can be seen in cloudwatch

FLow log can be created
  VPC level
  Subnet level
  Interface level 

Flow log requires the following  
  CLoudwatch log group or S3 bucket
  IAM rule 

Some traffics are not monitored in the VPC flow logs

- DNS traffic 
- Windows license
- DHCP traffic
- Traffic to reserved hosts 
- DHCP traffic 

Bastion hosts or jumpbox 
NAT host can't be a bastion host 

Direct connect is a common place / data center where it has connectivity to both AWS and to the client datacenter
Similar to edge location where it is connected to AWS region through dedicated network (backbone network)

## VPC Gateway


VPC endpoint (To connect to AWS services without going outside)
 interface endpoint (Supports multiple services) 
 gateway endpoint  (supports only S3 and dynamoDB)


Read FAQ for the load balancers

SNS - Simple notification service (push)
SQS - Simple queue service (pull)
SWF - Simple workflow 


## Kinesis streams (Stores the data for 24 hours)

Similar to sparc streaming data analysis
* consists of shards
* Stores the data for 24 hours

Kinesis firehose (online data analysis - no data storage)

Analysis can be done by lambda functions
* No data storage

Kinesis analysitics
used by streams/firehose for analysis 


Cheatsheet
https://tutorialsdojo.com/aws-cheat-sheet-amazon-elastic-compute-cloud-amazon-ec2/

