# DBmigration
Migrate database from AWS EC2 to RDS


Overview : 
1.	Created AWS RDS(MySQL) instance and accessed via EC2 instance.
2.	Created Replication Instance, Source Endpoint, Destination Endpoint.
3.	Created Database Migration Task and migrate Database from source database to destination database.
4.	Migrate MySQL database exists on the EC2 instance to AWS RDS Instance

Process Followed

I. Create an Amazon RDS Database

MySQL database
Region : N.Virginia
DB Details:
•	Database creation method : Standard
•	Engine options           :  MySQL
•	Version                       : Default
•	Templates                   : Free tier
•	DB instance identifier    :  dbinstance
•	Master username.         : dbsuser
•	Master password and Confirm password: Enter dbpassword321
•	DB instance class        : Burstable classes db.t2.micro — 1 vCPUs, 1 GiB RAM
•	Storage type            : General Purpose SSD (gp2)
•	Allocated storage        : 20 (default)
•	storage autoscaling     : Uncheck
•	Vitual Private Cloud(VPC) : Default VPC
•	Subnet group : Default
•	Public Access : No
•	VPC Security groups :  existing 
•	Existing VPC security group name : dbMigration-SG
Additional Configuration options
•	Initial database name: Enter myrdsdatabase
•	DB parameter group: default
•	Option group: default
•	automated backups: uncheck
•	Enhanced monitoring: uncheck
•	Auto minor version upgrade: uncheck
•	Maintenance window: No preference
•	Deletion protection: uncheck
•	Keep parameters as default. Scroll to the bottom of the page, 
Click Create database.
•	It will take around 5 minutes for the database to become available. Once the status changes from creating to the available, the database is ready.
•	Endpoint: dbinstance.c81x4bxxayay.us-east-1.rds.amazonaws.com


II. Replication instance in DMS

Database Migration Service under the Migration & Transfer.
Replication instance configuration section:
•	Name                             : dbreplicationinstance
•	Description                     : replicate EC2-Mysql to AWS RDS 
•	Instance class                : dms.t3.micro
•	Engine version               : Default
•	Allocated storage (GB)   : 10 GB
•	VPC                                : default VPC
•	Multi AZ                           :  Dev or test workload (Single-AZ)
•	Publicly accessible          : Check
Advanced security and network configuration section,
•	Replication subnet group  : Default
•	Availability zone                  : Default
•	VPC security group(s)        : Enter dbMigration-SG
•	KMS master key                  : Default
Leave other settings as default.
Click on the  Create button to create the replication instance.
It will take 5 minutes for replication instance to be created. Status will changes to available 
From dbreplicationinstance  Details section, private & public IP address need to be noted
•	Public IP address : 3.224.227.68
•	Private IP address : 172.31.29.148

III. Replication Instance configuration details in Source EC2 Instance
➢	SSH the Source EC2 instance.
➢	Switch to root user:  sudo su
➢	Login to the MySQL: -mysql –u root-p
➢	password: dbpassword321
➢	grant root access to the replication instance to connect with the MySQL server on 
•	GRANT ALL ON *.* TO root@'172.31.29.148' IDENTIFIED BY 'dbpassword321';
➢	same step with the Public Ip address of the replication instance.
•	GRANT ALL ON *.* TO root@'3.224.227.68' IDENTIFIED BY 'dbpassword321';
➢	Save the changes : FLUSH PROVILEGES;
➢	exit;
➢	restart the MySQL server :  /etc/init.d/mysql restart
➢	Replication instance has access for Source Instance MySQL Database. 

IV. Source Endpoints in DMS
Created the Source and Target endpoints for EC2 and RDS Instances. These endpoints will help to connect replication instance with both source and target machines. 
➢	Source Endpoint
•	N.Virginia (us-east-1) region.
•	Create an Endpoint, 
•	Follow the below steps to complete Endpoint type:
•	Select endpoint as 
•	RDS DB instance: Uncheck (This is for Source i.e Mysql on EC2)
➢	Endpoint configuration:
•	Endpoint identifier     : Enter sourcedbendpoint
•	Descriptive Amazon Resource Name (ARN): sourcedbendpoint
•	Source engine           : MySQL
•	Access to endpoint database:  Provide access information manually
•	Server name               : 54.221.120.52 - Public IP address of Source EC2 Instance
•	Port                             :  3306
•	Secure Socket Layer (SSL) mode: None
•	User name                 : root
•	Password                   : dbpassword321
3. other configurations as default.
4. endpoint connection test:
•	VPC                                   : Default
•	Replication instance          : dbreplicationinstance 
•	Run test to test the connection. If all are working fine, status will show “successful” . 
•	Create Endpoint.

V. Target Endpoint:
1.	To create an Endpoint, Click on the Endpoints(Left panel) in the DMS service console page and click on the  
2.	Follow the below steps to complete Endpoint type:
•	Select endpoint as  
•	Select RDS DB instance: check (This is for Target i.eRDS Instance)
•	Select RDS Database: dbinstance
3.	Endpoint configuration:
•	Endpoint identifier    : Enter dbinstance
•	Descriptive Amazon Resource Name (ARN): Enter awsrdsendpoint
•	Target engine            : MySQL
•	Access to endpoint database: Provide access information manually
•	Server name             : Enter dbinstance.c81x4bxxayay.us-east 1.rds.amazonaws.com ? 
•	Port                             : 3306
•	Secure Socket Layer (SSL) mode: None
•	User name                 : dbuser
•	Password                   : dbpassword321
4.	other settings as default.
5.	Test endpoint connection:
•	VPC                                   : Default
•	Replication instance          : Enter dbreplicationinstance 
•	Run test to test the connection. 
6.	Click on the Create endpoint  button.

VI. Checking AWS RDS Database before Migration
First, check the databases and tables that exist on the AWS RDS Instance. So that after migration, you will be able to find the new changes. We can use the existing Source EC2 Instance to connect to AWS RDS.
1.	SSH into the Source EC2 instance. 
2.	Switch to root user:  sudo su
3.	connect to the Amazon RDS Instance :
•	mysql -u dbinstance.c81x4bxxayay.us-east-1.rds.amazonaws.com -u dbuser -p
•	Enter Password: dbpassword321
4.	 show database;
 
VII. Create a Database Migration Task
An AWS Database Migration Service task is where all the migration process happens. We will specify the tables and schemas to use for the migration and any special processing, such as logging requirements, control table data, and error handling.
1.	Navigate to AWS DMS console and click on the Database migration tasks.
2.	Make sure you are in N.Virginia (us east-1) Region.
3.	Click on the  .
4.	Create a database migration task:
•	Task configuration:
•	Task identifier                         : Database-Migration-Task
•	Replication instance               : Select dbreplicationinstance 
•	Source database endpoint     : Enter sourcedbendpoint
•	Target database endpoint       : Enter dbinstance
•	Migration type                         : Migrate existing data and replicate ongoing changes
•	Task settings:
•	Editing mode: Wizard
•	Target table preparation mode           : Do nothing
•	Include LOB columns in replication: Limited LOB mode
•	Maximum LOB size (KB): 32 KB
•	Enable validation: Uncheck
•	Enable Cloudwatch Logs: Uncheck
•	Leave Advance tasks settings as default.
•	Table mappings:
•	Editing mode         : Wizard
•	Selection rules        :  
•	Schema        : Select Enter a Schema
•	Schema name        : Enter %SchoolDB (Database name)
•	Table name             : Enter %students (Table name)
•	Action                      : Include
•	Leave other settings as default.
5.	Click on Create task.
6.	Now the migration of Database will occur. Usually it will take around 1 minute in this lab.
VIII. Status of AWS Database Migration Tasks:
1.	Navigate to in left panel of DMS page.
2.	If you followed all the previous steps correctly, it will show the migration task status as 
3.	Now the Migration of database to Amazon RDS is completed.
4.	To check the migration status and details, we need to connect to destination MySQL database which is in AWS RDS Instance.
5.	SSH back into Source EC2 Instance.
6.	Connect to AWS RDS instance.
•	mysql -u dbinstance.c81x4bxxayay.us-east-1.rds.amazonaws.com -u awsrdsuser -p
•	Enter Password: whizlabs123
7.	Once inside MySQL Client, check the databases available.
•	show databases;
 
8.	Run command: 
•	use SchoolDB;
 
9.	Run command: 
•	show tables;
 
10.	Run command:
•	select * from students;
  
11.	Now we can see the SchoolDB which was available in Source EC2 Ubuntu Server migrated to Amazon RDS Instance Database.
![image](https://user-images.githubusercontent.com/53235392/227803008-88314c6c-4b06-418d-8d3a-9b2cce13d806.png)
