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

MySQL database
Region : <b> N.Virginia </b>
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



