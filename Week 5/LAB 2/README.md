# Working with RDS in AWS

Tasks:

1. Create an Amazon VPC for use with a DB instance
2.  Create a VPC with private and public subnets and additional subnets
3.  Create a VPC security group for a public web server
4.  Create a VPC security group for a private DB instance
5.  Create a DB subnet group
6.  Performn Clean up actions


    NB: If you fail to perform clean up actions, costs are usually incurred.



Guide

https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/TUT_WebAppWithRDS.html

https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_Tutorials.html



> # Notes from lab Tasks

> ## 1. Create an Amazon VPC for use with a DB instance

NOTE: This LAB describes configuring a VPC for Amazon RDS DB instances

To create a VPC and subnets

If you don't have an Elastic IP address to associate with a network address translation (NAT) gateway, allocate one now. A NAT gateway is required for this tutorial. If you have an available Elastic IP address, move on to the next step.

Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/.

In the top-right corner of the AWS Management Console, choose the Region to allocate your Elastic IP address in. The Region of your Elastic IP address should be the same as the Region where you want to create your VPC. This lab uses the US East (North Virginia) Region.

![Imgur](https://imgur.com/plHNC61.jpg)

* In the navigation pane, choose Elastic IPs.

![Imgur](https://imgur.com/gTyXiFN.jpg)

* Choose Allocate Elastic IP address.

* If the console shows the Network Border Group field, keep the default value for it.

* For Public IPv4 address pool, choose Amazon's pool of IPv4 addresses.

* Choose Allocate.

![Imgur](https://imgur.com/v2yGoDJ.jpg)


Note the allocation ID of the new Elastic IP address because you'll need this information when you create your VPC.

![Imgur](https://imgur.com/xa3hnMi.jpg)


> ## 2.  Create a VPC with private and public subnets and multiple subnets

Open the Amazon VPC console at https://console.aws.amazon.com/vpc/.

Make sure that you default region still remains us-east or whatever default region you choose.

To begin creating a VPC, choose Launch VPC Wizard.

In the step 1: Select a VPC Configuration page, choose VPC with Public and Private Subnets, and then choose Select.

On the Step 2: VPC with Public and Private Subnets page, set these values:

* IPv4 CIDR block: 10.0.0.0/16

* IPv6 CIDR block: No IPv6 CIDR Block

* VPC name: Demo-vpc

* Public subnet's IPv4 CIDR: 10.0.0.0/24

* Availability Zone: us-east-1a

* Public subnet name: demo-public

* Private subnet's IPv4 CIDR: 10.0.1.0/24

* Availability Zone: us-east-1b

* Private subnet name: demo-private-1

* Elastic IP Allocation ID: An Elastic IP address to associate with the NAT gateway

* Service endpoints: Skip this field.

* Enable DNS hostnames: Yes

* Hardware tenancy: Default

* Choose Create VPC.

![Imgur](https://imgur.com/NIcGzoF.jpg)

![Imgur](https://imgur.com/Y4q5yZh.jpg)

![Imgur](https://imgur.com/1wjistc.jpg)



## Create Additional subnets
You must have either two private subnets or two public subnets available to create a DB subnet group for a DB instance to use in a VPC. Because the DB instance for this tutorial is private, add a second private subnet to the VPC.

To create an additional private subnet

Navigate back to  the Amazon VPC Dashboard, choose Subnets, and then choose Create subnet.

On the Create subnet page, set these values:

* VPC ID: Choose the VPC that you created in the previous step,

* Subnet name: demo-private-2

* Availability Zone: us-east-1c

* Choose an Availability Zone that is different from the one that you chose for the first private subnet.

* IPv4 CIDR block: 10.0.2.0/24

* Choose Create subnet.

![Imgur](https://imgur.com/mGLLmmP.jpg)

To ensure that the second private subnet that you created uses the same route table as the first private subnet, complete the following steps:

from the VPC Dashboard, click on Subnets, and then select the first private subnet that you created for the VPC, demo-private 1.

Below the list of subnets, choose the Route table tab, and note the value for Route Table ID

![Imgur](https://imgur.com/bvuiLQC.jpg)

THEN, deselect the first private subnet.

Afterwards, select the second private subnet demo-private 2, and click the Route table tab.

If the current route table is not the same as the route table for the first private subnet, choose Edit route table association. For Route table ID, choose the route table that you noted earlier.

![Imgur](https://imgur.com/Ld4rd9O.jpg)


Next, to save your selection, choose Save.

> ## 3.  Create a VPC security group for a public web server


To create a VPC security group

Navigate to the VPC Dashboard, choose Security Groups, and then choose Create security group.

![Imgur](https://imgur.com/hxW0zvh.jpg)


On the Create security group page, set these values:

* Security group name: demo-security-group

* Description: Demo Security Group

* VPC: Choose the VPC ID that you created earlier

####  Add inbound rules to the security group

Warning

If you use 0.0.0.0/0, you enable all IP addresses to access your public instances. This approach is acceptable for a short time in a test environment, but it's unsafe for production environments. In production, you'll authorize only a specific IP address or range of addresses to access your instances.

In the Inbound rules section, chose the parameters as shown in the image below

![Imgur](https://imgur.com/acAnVzW.jpg)

Then, click Create security group.

Note the security group ID because you need it later in this lab.


> ##  4.  Create a VPC security group for a private DB instance

To keep your DB instance private, create a second security group for private access. To connect to private instances in your VPC, you add inbound rules to your VPC security group that allow traffic from your web server only.

#### To Create the Security Group

navigate to the VPC Dashboard, choose Security Groups, and then choose Create security group.

On the Create security group page, set these values:

* Security group name: demo-db-security-group

* Description: Demo DB Instance Security Group

* VPC: Choose the VPC that you created earlier

Add inbound rules to the security group.

In the Inbound rules section, choose Add rule.

Set the following values for your new inbound rule to allow MySQL traffic on port 3306 from your EC2 instance. If you do this, you can connect from your web server to your DB instance to store and retrieve data from your web application to your database.

* Type: MySQL/Aurora

* Source: The ID of the demo-security-group that you created previously in this lab

![Imgur](https://imgur.com/z8dyrIR.jpg)


Then, Click Create security group.


> ## 5.  Create a DB subnet group

A DB subnet group is a collection of subnets that you create in a VPC and that you then designate for your DB instances. A DB subnet group allows you to specify a particular VPC when creating DB instances.

To create a DB subnet group

Open the Amazon RDS console


In the navigation pane, choose Subnet groups.

Choose Create DB Subnet Group.

On the Create DB subnet group page, set these values in Subnet group details:

* Name: demo-db-subnet-group

* Description: Demo DB Subnet Group

* VPC: demo-vpc (vpc-id)

In the Add subnets section, choose the Availability Zones and Subnets.

For this lab, choose us-east-1b and us-east-1c for the Availability Zones. Next, for Subnets, choose the subnets for IPv4 CIDR block 10.0.1.0/24 and 10.0.2.0/24.


![Imgur](https://imgur.com/ew7AMDI.jpg)
![Imgur](https://imgur.com/YN8zQEd.jpg)




Choose Create.

Your new DB subnet group appears in the DB subnet groups list on the RDS console. You can click the DB subnet group to see details, including all of the subnets associated with the group, in the details pane at the bottom of the window.

![Imgur](https://imgur.com/5lGYKb5.jpg)

> ## Create a DB instance

In this step, you create an Amazon RDS for MySQL DB instance that maintains the data used by a web application.

To create a MySQL DB instance

Open the Amazon RDS console at https://console.aws.amazon.com/rds/.

In the upper-right corner of the Management Console, make sure that you are still in the US East (North Virginia) Region.

In the navigation pane, choose Databases.

Choose Create database.

On the Create database page, shown following, make sure that the Standard create option is chosen, and then choose MySQL

![Imgur](https://imgur.com/CR4Nz7Z.jpg)

In the Templates section, choose Free tier.

In the Settings section, set these values:

* DB instance identifier – demo-db-instance

* Master username – demo_user

* Auto generate a password – Clear the check box.

* Master password – Choose a password.

* Confirm password – Retype the password.

![Imgur](https://imgur.com/77OjBiD.jpg)

In the DB instance class section, enable Include previous generation classes, and set these values:

* Burstable classes (includes t classes)

* db.t2.micro


![Imgur](https://imgur.com/Ip2Wp0M.jpg)

In the Storage and Availability & durability sections, use the default values.

In the Connectivity section, set these values:

* Virtual private cloud (VPC) – Choose an existing VPC with both public and private subnets, such as the demo-vpc (vpc-id) created previously

Note

The VPC must have subnets in different Availability Zones.

* Subnet group – The DB subnet group for the VPC, such as the demo-db-subnet-group created previously

* Public access – No

* VPC security group –  Choose an existing VPC security group that is configured for private access such as the demo-db-securitygroup created 


* Availability Zone – No preference

* Open Additional configuration, and make sure Database port uses the default value 3306.



![Imgur](https://imgur.com/QCQ5mU7.jpg)


In the Database authentication section, make sure Password authentication is selected.

Open the Additional configuration section, and enter sample for Initial database name. Keep the default settings for the other options.

Then, choose Create database.

Your new DB instance appears in the Databases list with the status Creating.

Wait for the Status of your new DB instance to show as Available. Then choose the DB instance name to show its details.

In the Connectivity & security section, view the Endpoint and Port of the DB instance.


![Imgur](https://imgur.com/mgohvH2.jpg)


Note the endpoint and port for your DB instance. You use this information to connect your web server to your DB instance.



> ## 6.  Performn Clean up actions

* Delete the DB subnet group from the RDS console
* Delete the security groups.
* Delete the VPC.
* Delete the NAT gateway created
* Release the Elastic IP addresses.
* Delete te Database iIstance

                