<h1>Configuring RDS for High Availability and Read Scalability</h1>

<h2>Description</h2>

This project demonstrates how I configured an Amazon Relational Database Service (Amazon RDS) environment to reduce database administration overhead while improving availability, resilience, and read performance.

An insurance company experienced several database challenges:

<ul>
  <li>Database administrators spent too much time patching and managing database infrastructure.</li>
  <li>The company needed improved database availability and disaster recovery.</li>
  <li>Large analytics queries created heavy read loads on the primary database.</li>
  <li>The company wanted a simpler way to migrate existing databases into AWS.</li>
</ul>

To address these requirements, I deployed an Amazon RDS MariaDB database with Multi-AZ support, enabled storage autoscaling, reviewed automated backup options, and explored Amazon RDS read replicas and AWS Database Migration Service (AWS DMS).

<br />

<h2>Architecture Overview</h2>

<ul>
<li><b>Amazon RDS Primary DB Instance</b> handling the main database workload and write operations.</li>
<li><b>Amazon RDS Read Replica</b> supporting read-intensive workloads such as analytics queries.</li>
<li><b>Amazon RDS Multi-AZ Standby</b> providing high availability and automatic failover.</li>
<li><b>Multiple Availability Zones</b> improving database resilience.</li>
<li><b>AWS Database Migration Service (AWS DMS)</b> supporting database discovery, migration, and replication.</li>
</ul>

<p align="center">
<img width="1000" height="550" alt="Amazon RDS Multi-AZ Architecture" src="https://github.com/user-attachments/assets/45c863f8-1598-4157-a5a2-ae3cfe481705" />

<h2>Architecture Design</h2>

<ul>
<li>The primary Amazon RDS DB instance handles the main database workload and write operations.</li>
<li>A read replica can be used to handle analytics queries and other read-intensive workloads.</li>
<li>A standby RDS instance is deployed in another Availability Zone using Multi-AZ.</li>
<li>The standby database receives synchronous replication from the primary database.</li>
<li>If the primary database becomes unavailable, Amazon RDS can automatically fail over to the standby instance.</li>
<li>Public access is disabled to prevent direct database exposure to the internet.</li>
</ul>
</p>

<h2>Technologies Used</h2>

* <b>Amazon RDS</b>
* <b>MariaDB</b>
* <b>Amazon EC2</b>
* <b>Amazon VPC</b>
* <b>AWS Database Migration Service (AWS DMS)</b>
* <b>Amazon RDS Multi-AZ</b>
* <b>Amazon RDS Read Replicas</b>
* <b>General Purpose SSD (gp3)</b>
* <b>AWS Management Console</b>

<h2>Environments Used</h2>

* <b>AWS Console</b>

<h2>Project Objectives</h2>

<ul>
<li>Reduce database infrastructure management and patching responsibilities.</li>
<li>Deploy a managed relational database using Amazon RDS.</li>
<li>Configure Multi-AZ deployment for high availability and disaster recovery.</li>
<li>Configure storage autoscaling to support future database growth.</li>
<li>Understand how read replicas improve read-heavy workload performance.</li>
<li>Review automated database backup options.</li>
<li>Explore AWS Database Migration Service for database migration and replication.</li>
</ul>

<h2>Walk-through</h2>

<p align="center">

<b>Step 1: Review Database Options in Amazon EC2.</b><br/>
Opened EC2 service and navigated to the AMI Catalog. Searched for <b>SQL</b> to review database-related Amazon Machine Images available through AWS, the AWS Marketplace, and the AWS community. <br/><br/>

This demonstrated that databases can be self-hosted on EC2 instances, but this approach requires administrators to manage the operating system, patching, database software, and infrastructure. <br/><br/>
<b>Step 2: Open RDS and Create a Database.</b><br/>
Navigated to Amazon RDS and selected <b>Create database</b>. <br/><br/>

Configured the database using:

<ul>
<li>Creation method: <b>Standard create</b></li>
<li>Engine type: <b>MariaDB</b></li>
<li>Template: <b>Dev/Test</b></li>
<li>DB instance identifier: <b>my-database</b></li>
<li>Master username: <b>admin</b></li>
<li>Credentials management: <b>Self managed</b></li>
</ul>

The database password is intentionally excluded from this repository. <br/><br/>

<img width="900" alt="Amazon RDS Database Creation" src="https://github.com/user-attachments/assets/c63dbbc7-82bd-4867-911f-2a05eeb37d8a" />

<br/><br/>

<b>Step 3: Configure the Database Instance.</b><br/>
Configured the Amazon RDS database instance using:

<ul>
<li>DB instance class: <b>Burstable classes</b></li>
<li>Instance type: <b>db.t3.xlarge</b></li>
<li>Storage type: <b>General Purpose SSD (gp3)</b></li>
<li>Allocated storage: <b>20 GiB</b></li>
</ul>

This configuration provided a managed database environment while reducing the operational work required to maintain the underlying database infrastructure. <br/><br/>

<img width="900" alt="Amazon RDS Instance Configuration" src="https://github.com/user-attachments/assets/278837cd-1ec9-4e31-8d8c-fac7ab961f4c" />

<br/><br/>

<b>Step 4: Configure Storage Autoscaling and Multi AZ Deployment.</b><br/>
Expanded the additional storage configuration and enabled storage autoscaling. I also enabled a standby database instance using RDS Multi-AZ deployment. <br/><br/>
Configured:

<ul>
<li>Storage autoscaling: <b>Enabled</b></li>
<li>Maximum storage threshold: <b>1000 GiB</b></li>
<li>Create a standby instance</b></li>
<b></b></li>
</ul>

Storage autoscaling allows Amazon RDS to automatically increase database storage as capacity requirements grow.
Multi-AZ provides high availability by maintaining a synchronously replicated standby database in another Availability Zone.
If the primary database becomes unavailable, Amazon RDS can automatically fail over to the standby database. <br/><br/>

<img width="900" alt="Amazon RDS Storage Autoscaling" src="https://github.com/user-attachments/assets/b180b5a4-bef9-4a19-9285-ecba9cadf89e" />

<br/><br/>

<b>Step 5: Configure Database Networking.</b><br/>
Reviewed the networking configuration for the Amazon RDS database. <br/><br/>

Configured:

<ul>
<li>VPC: <b>Default VPC</b></li>
<li>DB subnet group: <b>Default</b></li>
<li>Public access: <b>No</b></li>
<li>VPC Security Group: <b>Existing Security Group</b></li>
</ul>

Disabling public access helps protect the database by preventing direct connections from the public internet. <br/><br/>

<img width="900" alt="Amazon RDS Networking Configuration" src="https://github.com/user-attachments/assets/6aefa687-0991-4c3c-a4f5-fc9687d3f001" />

<br/><br/>

<b>Step 6: Configure Monitoring.</b><br/>
Reviewed the available RDS monitoring options. <br/><br/>

Configured:

<ul>
<li>Database Insights: <b>Standard</b></li>
<li>Performance Insights: <b>Disabled</b></li>
<li>Enhanced Monitoring: <b>Disabled</b></li>
</ul>
<br/><br/>

<b>Step 7: Configure the Initial Database.</b><br/>
Expanded the additional configuration section and created the initial database. <br/><br/>

Configured:

<ul>
<li>Initial database name: <b>my_database</b></li>
</ul>

The database name is separate from the RDS DB instance identifier. <br/><br/>

The RDS DB instance identifier was:
<ul>
<li><b>my-database</b></li>
</ul>
The database name was:
<ul>
<li><b>my_database</b></li>
</ul>
<br/><br/>

<b>Step 8: Review Backup, Encryption, and Maintenance Settings.</b><br/>
Reviewed the Amazon RDS backup and encryption options before deployment. <br/><br/>

Configured:

<ul>
<li>Automated backups: <b>Enabled</b></li>
<li>Encryption: <b>Enabled</b></li>
<li>Automatic minor version upgrade: <b>Disabled</b></li>
<li>Maintenance window: <b>No preference</b></li>
</ul>

Automated backups allow database recovery using stored backup data and help protect against accidental data loss. <br/><br/>

<br/><br/>

<b>Step 9: Create the Amazon RDS Database.</b><br/>
Reviewed the database configuration and selected <b>Create database</b>. <br/><br/>
Amazon RDS began provisioning the MariaDB database instance and its associated Multi-AZ standby instance. <br/><br/>

The provisioning process took several minutes to complete. <br/><br/>
<br/><br/>

<b>Step 10: Understand the Purpose of Read Replicas.</b><br/>
Reviewed how Amazon RDS read replicas can improve performance for read-intensive workloads, then followed a similar configuration process to create a read replica for the primary database.
<br/><br/>

<img width="900" alt="Amazon RDS Networking Configuration" src="https://github.com/user-attachments/assets/aae850ca-7413-420f-b76a-e74ef89ef863" />
<br/><br/>
The insurance company's Data Analytics team frequently runs large analytics queries. Instead of sending every read request to the primary database, these workloads can be directed to a read replica. <br/><br/>

This allows:

<ul>
<li>The primary database to focus on write operations.</li>
<li>Read replicas to process large analytics and reporting queries.</li>
<li>Database workloads to scale more efficiently.</li>
</ul>

It is important to distinguish between Multi-AZ and read replicas:

<ul>
<li><b>Multi-AZ</b> is primarily used for high availability and failover.</li>
<li><b>Read Replicas</b> are primarily used for read scalability and performance.</li>
</ul>
<br/><br/>

<h2>High Availability Improvements</h2>

<ul>
<li>Configured Amazon RDS instead of relying on self-managed database infrastructure.</li>
<li>Enabled Multi-AZ deployment to maintain a standby database in another Availability Zone.</li>
<li>Used synchronous replication between the primary and standby databases.</li>
<li>Enabled automated backups to improve database recoverability.</li>
<li>Enabled storage autoscaling to support future database growth.</li>
</ul>

<h2>Performance Improvements</h2>

<ul>
<li>Designed the architecture to support Amazon RDS read replicas.</li>
<li>Separated read-intensive analytics workloads from primary database write operations.</li>
<li>Reduced potential performance impact caused by large reporting and analytics queries.</li>
</ul>

<h2>Lessons Learned</h2>

During this project, I gained hands-on experience configuring and deploying a managed relational database using Amazon RDS. I learned how Amazon RDS reduces the operational workload associated with patching and infrastructure management, how Multi-AZ deployments provide high availability through synchronous replication and automatic failover, and how read replicas can improve performance for read-intensive workloads.

I also learned that Multi-AZ and read replicas serve different purposes. Multi-AZ is primarily designed for high availability and disaster recovery, while read replicas are designed to improve read scalability. Additionally, I explored AWS Database Migration Service and learned how it can help migrate and replicate databases while minimizing application downtime.

<h2>Skills Demonstrated</h2>

<ul>
<li>Amazon RDS Configuration</li>
<li>MariaDB Deployment</li>
<li>Multi-AZ Database Architecture</li>
<li>Database High Availability</li>
<li>Disaster Recovery</li>
<li>Read Replica Architecture</li>
<li>Database Performance Optimization</li>
<li>Storage Autoscaling</li>
<li>Amazon VPC Networking</li>
<li>Automated Database Backups</li>
<li>AWS Database Migration Service</li>
<li>Cloud Database Administration</li>
<li>AWS Architecture Design</li>
</ul>
