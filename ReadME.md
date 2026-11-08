3-tier application
----------------------
Github URL

**Steps_for_Setting_up_the_Project_Infrastructure**
-----------------------------------------------------
1.VPC Creation
2.S3 Bucket and IAM Role Setup
3.Database Configuration
4.Application Tier Setup
5.Web Tier Setup
6.SSL certification and Domain Mapping (optional)




Database configuration-
install mysql
sudo dnf install mariadb105 -y        --->mysql client

configure sql database 
mysql -h <DB EndPoint> -u admin -p -----------> Enter the passwd i.e admin(this is DB passwd). If u couldn't connect, there is a problem with the SG of the DB

how to get database endpoint ---> go to RDS Service --->Databases (left panel) 

ex : mysql -h database-1.c7wgcik0elb2.us-east-2.rds.amazonaws.com -u admin -p

if issue go to Security Groups -->modify inbound rules
 mysql starts

 CREATE DATABASE webappdb;

 SHOW DATABASES;
 USE webappdb;   -----> u will see "Database changed"

 Execute the belowcode as a single code. here we r creating a table with the name 'transactions'
 CREATE TABLE IF NOT EXISTS transactions(
 id INT NOT NULL AUTO_INCREMENT,
 amount DECIMAL(10,2),
 description VARCHAR(100),
 PRIMARY KEY(id)
);

To verify whether table got created or not;
SHOW TABLES;

Lets insert some info into the table
INSERT INTO transactions (amount, description) VALUES ('400', 'groceries');

To verify whether the entry is really created or not
SELECT * FROM transactions;
You will see the info you have written

To come out of the DB;
exit (You will see 'ec2-user' at the end of command line and at the beginning of command line you will see 'root')

_________________-__App tier to connect database__-_____
go to the download github 3 tier folder --> go to apptier --> select DbConfig.js -->modify in vscode
Update Application Configuration to with DB information
Update the application-code/app-tier/DbConfig.js file with your database credentials.
go to s3Bucket then select the project bucket <3tierProjett> 

install and configure node.js and PM2
curl -o- https://raw.githubusercontent.com/avizway1/aws_3tier_architecture/main/install.sh | bash
source ~/.bashrc

nvm install 26
nvm use 26 (You will see 'Now using node v26.20.2)
NVM means Node Version Manager

 To run node as a service, we will install pm2
sudo dnf install libatomic -y
npm install -g pm2 (You will see 'found 0 vulnerabilities)

Download application code from S3 and start the application
cd ~/
sudo aws s3 cp s3:///application-code/app-tier/ app-tier --recursive
Ex: sudo aws s3 cp s3://demo-3tier-projett/application-code/app-tier/ app-tier --recursive
ls ---> You will see 'app-tier' folder

cd app-tier/
npm install
ls ----> You will see 'index.js' file. We have to start that.
pm2 start index.js (You will see the status as 'online')
To verify;
pm2 list (or) pm2 status
pm2 logs (You will not see anything in red colour, everything in white colour you should see)

At the end you will see something like; http://localhost:4000
pm2 startup
pm2 save ---> To save the configuration

Verify that the application is running by executing
curl http://localhost:4000/health
It should return: This is the health check.
========================================= App configuration ++++++++++++++++++++++++



With this we have completed the application configuration.
4.2. Creation of Internal Load Balancer for App Tier
Goto the downloaded code folder in local system ----> Open nginx.conf file and in the end of the file you will see something like below;
#proxy for internal lb
location /api/{
proxy_pass http://[REPLACE-WITH-INTERNAL-LB-DNS]:80/;



               
