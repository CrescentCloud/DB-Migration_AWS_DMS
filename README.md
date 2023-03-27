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
From dbreplicationinstance  Details section, private & public IP address need to be noted
•	Public IP address : <b>3.224.227.68</b><br>
•	Private IP address :<b> 172.31.29.148</b><br>
