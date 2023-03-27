# DBmigration
## Migrate MySQL database from AWS EC2 to RDS


### Overview : 
1.	Created AWS RDS(MySQL) instance and accessed via EC2 <b>instance</b>.
2.	Created Replication Instance, Source Endpoint, Destination Endpoint.
3.	Created Database Migration Task and migrate Database from source database to destination database.
4.	Migrate MySQL database that exists on the EC2 instance to AWS RDS Instance
![DBMigration_Diagram](https://user-images.githubusercontent.com/53235392/227803558-daaa553b-11dd-4599-b597-d91ef689d6fc.png)


## Detail Process
### I. Create an Amazon RDS Database

MySQL database <br>
Region : <b> N.Virginia </b><br>
 </b><br>
•	Database creation method : <b>Standard</b><br>
•	Engine options           :  <b>MySQL</b><br>
•	Version                       : <b>Default</b><br>
•	Templates                   : <b>Free tier</b><br>
•	DB instance identifier    :  <b>dbinstance</b><br>
•	Master username.         : <b>dbsuser</b><br>
•	Master password and Confirm password: dbpassword321</b><br>
•	DB instance class        : <b>Burstable classes db.t2.micro — 1 vCPUs, 1 GiB RAM</b><br>
•	Storage type            : <b>General Purpose SSD (gp2)</b><br>
•	Allocated storage        : <b>20 (default)</b><br>
•	storage autoscaling     : <b>Uncheck</b><br>
•	Vitual Private Cloud(VPC) : <b>Default VPC</b><br>
•	Subnet group : <b>Default</b><br>
•	Public Access : <b>No</b><br>
•	VPC Security groups :  <b>existing </b><br>
•	Existing VPC security group name : <b>dbMigration-SG</b><br>
Additional Configuration options<br>
•	Initial database name: <b>Enter myrdsdatabase</b><br>
•	DB parameter group: <b>default</b><br>
•	Option group: <b>default</b><br>
•	automated backups: <b>uncheck</b><br>
•	Enhanced monitoring: <b>uncheck</b><br>
•	Auto minor version upgrade: <b>uncheck</b><br>
•	Maintenance window: <b>No preference</b><br>
•	Deletion protection: <b>uncheck</b><br>
Keep parameters as default. Scroll to the bottom of the page, </b><br>
Click Create database.</b><br>
•	It will take around 5 minutes for the database to become available. Once the status changes from creating to the available, the database is ready.</b><br>
•	Endpoint: <b>dbinstance.c81x4bxxayay.us-east-1.rds.amazonaws.com</b><br><br>

### II. Replication instance in DMS

Database Migration Service under the Migration & Transfer.<br>
Replication instance configuration section:

•	Name                             : <b>dbreplicationinstance</b><br>
•	Description                     : <b>replicate EC2-Mysql to AWS RDS</b><br> 
•	Instance class                : <b>dms.t3.micro</b><br>
•	Engine version               : <b>Default</b><br>
•	Allocated storage (GB)   : <b>10 GB</b><br>
•	VPC                                : <b>default VPC</b><br>
•	Multi AZ                           :  <b>Dev or test workload (Single-AZ)</b><br>
•	Publicly accessible          : <b>Check</b><br>
Advanced security and network configuration section,
•	Replication subnet group  : <b>Default</b><br>
•	Availability zone                  : <b>Default</b><br>
•	VPC security group(s)        : <b>Enter dbMigration-SG</b><br>
•	KMS master key                  : <b>Default</b><br>
Leave other settings as default.
Click on the  Create button to create the replication instance.
It will take 5 minutes for replication instance to be created. Status will changes to available 
From dbreplicationinstance  Details section, private & public IP address need to be noted<br>
•	Public IP address : <b>3.224.227.68</b><br>
•	Private IP address :<b> 172.31.29.148</b><br>

### III. Replication Instance configuration details in Source EC2 Instance
SSH the Source EC2 instance.<br>
•	Switch to root user:  <b>sudo su</b><br>
•	Login to the MySQL: <b>-mysql –u root-p</b><br>
•	password: <b>dbpassword321</b><br>
grant root access to the replication instance to connect with the MySQL server on <br>
•	<b>GRANT ALL ON *.* TO root@'172.31.29.148' IDENTIFIED BY 'dbpassword321';</b><br>
same step with the Public Ip address of the replication instance.<br>
•	<b>GRANT ALL ON *.* TO root@'3.224.227.68' IDENTIFIED BY 'dbpassword321';</b><br>
•	Save the changes : <b>FLUSH PROVILEGES;</b><br>
•	exit;<br>
•	restart the MySQL server :  <b>/etc/init.d/mysql restart</b><br>
Replication instance has access for Source Instance MySQL Database.<br><br> 

### IV. Source Endpoints in DMS
Created the Source and Target endpoints for EC2 and RDS Instances. These endpoints will help to connect replication instance with both source and target machines. <br>
Source Endpoint<br>
•	<b> N.Virginia (us-east-1) region.</b><br>
•	Create an Endpoint, <br>
•	Follow the below steps to complete Endpoint type:<br>
•	Select endpoint as <br>
•	RDS DB instance: <b> Uncheck (This is for Source i.e Mysql on EC2)</b><br>
Endpoint configuration:<br>
•	Endpoint identifier     : <b>Enter sourcedbendpoint</b><br>
•	Descriptive Amazon Resource Name (ARN): <b>sourcedbendpoint</b><br>
•	Source engine           : <b>MySQL</b><br>
•	Access to endpoint database:  <b>Provide access information manually</b><br>
•	Server name               : <b>54.221.120.52 - Public IP address of Source EC2 Instance</b><br>
•	Port                             :  <b>3306</b><br>
•	Secure Socket Layer (SSL) mode: <b>None</b><br>
•	User name                 : <b>root</b><br>
•	Password                   : <b>dbpassword321</b><br>
other configurations as default.<br>
endpoint connection test:<br>
•	VPC                                   : <b>Default</b><br>
•	Replication instance          : <b>dbreplicationinstance </b><br>
•	Run test to test the connection. If all are working fine, status will show “successful” .<br> 
•	Create Endpoint.<br><br>

### V. Target Endpoint:
To create an Endpoint, Click on the Endpoints(Left panel) in the DMS service console page and click on the <br> 
Follow the below steps to complete Endpoint type:<br>
•	endpoint as<br>  
•	RDS DB instance: <b>check (This is for Target i.eRDS Instance)</b><br>
•	RDS Database: <b>dbinstance</b><br>
Endpoint configuration:<br>
•	Endpoint identifier    : <b>Enter dbinstance</b><br>
•	Descriptive Amazon Resource Name (ARN): <b>Enter awsrdsendpoint</b><br>
•	Target engine            : <b>MySQL</b><br>
•	Access to endpoint database: <b>Provide access information manually</b><br>
•	Server name             : <b>Enter dbinstance.c81x4bxxayay.us-east 1.rds.amazonaws.com ?</b><br> 
•	Port                             : <b>3306</b><br>
•	Secure Socket Layer (SSL) mode: <b>None</b><br>
•	User name                 : <b>dbuser</b><br>
•	Password                   : <b>dbpassword321</b><br>
other settings as default.<br>
Test endpoint connection:<br>
•	VPC                                   : <b>Default</b><br>
•	Replication instance          : <b> dbreplicationinstance </b><br>
Run test to test the connection.<br>
Click on the Create endpoint  button.<br><br>

### VI. Checking AWS RDS Database before Migration
First, check the databases and tables that exist on the AWS RDS Instance. So that after migration, you will be able to find the new changes. We can use the existing Source EC2 Instance to connect to AWS RDS.<br>
SSH into the Source EC2 instance.<br>
Switch to root user: <b> sudo su</b><br>
connect to the Amazon RDS Instance : <b> mysql -u dbinstance.c81x4bxxayay.us-east-1.rds.amazonaws.com -u dbuser -p </b><br>
•	Password: <b>dbpassword321</b><br>
<b>show database;</b><br>
 
### VII. Create a Database Migration Task
An AWS Database Migration Service task is where all the migration process happens. We will specify the tables and schemas to use for the migration and any special processing, such as logging requirements, control table data, and error handling.<br>
Navigate to AWS DMS console and click on the Database migration tasks.<br>
Make sure you are in N.Virginia (us east-1) Region.<br>
Create a database migration task:<br>
#### Task configuration:<br>
•	Task identifier                         : <b>Database-Migration-Task</b><br>
•	Replication instance               : <b>Select dbreplicationinstance</b><br> 
•	Source database endpoint     : <b>sourcedbendpoint</b><br>
•	Target database endpoint       : <b>dbinstance</b><br>
•	Migration type                         : <b>Migrate existing data and replicate ongoing changes</b><br>
#### Task settings:<br>
•	Editing mode: Wizard
•	Target table preparation mode           : <b>Do nothing</b><br>
•	Include LOB columns in replication: <b>Limited LOB mode</b><br>
•	Maximum LOB size (KB): <b>32 KB</b><br>
•	Enable validation: <b>Uncheck</b><br>
•	Enable Cloudwatch Logs: <b>Uncheck</b><br>
#### Leave Advance tasks settings as default.<br>
•	Table mappings:<br>
•	Editing mode         : <b>Wizard</b><br>
#### Selection rules        :  
•	Schema        : <b>Select Enter a Schema</b><br>
•	Schema name        : <b>Enter %SchoolDB (Database name)</b><br>
•	Table name             : <b>Enter %students (Table name)</b><br>
•	Action                      : <b>Include</b><br>
#### Leave other settings as default.<br>
#### Click on Create task.
Now the migration of Database will occur. Usually it will take around 1-3 minute depending on how big the database is <br><br>

### VIII. Status of AWS Database Migration Tasks:
Now the Migration of database to Amazon RDS is completed.<br>
To check the migration status and details, we need to connect to destination MySQL database which is in AWS RDS Instance.<br>
SSH back into Source EC2 Instance.<br>
Connect to AWS RDS instance.<br>
•	<b>mysql -u dbinstance.c81x4bxxayay.us-east-1.rds.amazonaws.com -u dbuser -p </b><br>
•	Password: <b>dbpassword321 </b><br> 
Once inside MySQL Client, check the databases available.<br>
<b>show databases;</b><br>
<b>show tables;</b><br>
  
#### Now we can see the Database which was available in Source EC2 Ubuntu Server migrated to Amazon RDS Instance Database.





