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
Additional Configuration options</b><br>
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
•	Endpoint: <b>dbinstance.c81x4bxxayay.us-east-1.rds.amazonaws.com</b><br>
