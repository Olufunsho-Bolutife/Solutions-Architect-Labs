Task: Create a nondefault VPC using AWS CLI

1. Open your CLI and run a configuration setting
2. Lauch a VPC 
3. Create two subnets (a public and a private subnet)
4. Make your subnet public by creating and attaching an internet gateway
5. Create a security group and add a SSH access from anywhere
6. Lauch an instance into your subnet 
7. Clean Up

https://docs.aws.amazon.com/vpc/latest/userguide/vpc-subnets-commands-example.html

https://docs.aws.amazon.com/vpc/latest/userguide/vpc-subnets-commands-example-ipv6.html

https://docs.aws.amazon.com/vpc/index.html

# Notes from the lab work

----------


> ## Open your CLI and run a configuration setting

You need to run an "aws configure" command so that your local terminal can cmmunicate with your aws account in the aws console.

To do this run the aws configure command and fill in security details as required

NOTE: I have the aws CLI downloaded on my windows machine so I will be accesing the aws console though my windows terminal 

    aws configure

![Imgur](https://imgur.com/zug5C0o.jpg)

You can get these security credentials from your IAM profile
create new acces keys and download the csv file because you only get shot to access the secret key ID

![Imgur](https://imgur.com/AzJXAyN.jpg)

> ## Lauch a VPC 
In the previous labs we explicitly descibed how to launch a non default VPC using the management console but now, we want to take the CLI route.

To launch a non default vpc using aws cli you will run the command below

    aws ec2 create-vpc \
    --cidr-block 10.0.0.0/16

![Imgur](https://imgur.com/eYRjJpl.jpg)

Make sure you take note of all the information being given in the JSON responses. The JSON contains important information like the ID of our VPC components which we will be referencing as we move on

> ## Create two subnets (a public and a private subnet)
You need to create two subnets, one in which you will launch your public resources and the other in which you will launch your private resources.

To create the public subnet, run the command below
    
    aws ec2 create-subnet \
    --vpc-id vpc-05c375ae9942ef1b0 \
    --cidr-block 10.0.1.0/24 \

![Imgur](https://imgur.com/jZCKJ7f.jpg)

Take note of the JSON response you will need the information from it

By default, the subnets we create are private i.e resources launched into them will not recieve a public Ip address and therefore no means of communication with the public. For this reason we will run a command to enable resources launcehd into this subnet to receive public IPv4 addresses enabling them to communicate with the public. This command  will be shown in step 4


To create our private subnet we follow the same step as we did to create the public and we will leave the subnet as it is not enabling it to map out public IPv4 addresses to resoures launched in it

    aws ec2 create-subnet \
    --vpc-id vpc-05c375ae9942ef1b0 \
    --cidr-block 10.0.2.0/24 \

Kindly, note that the CIDRs must not overlap

![Imgur](https://imgur.com/RioVELm.jpg)


>## Make your subnet public by creating and attaching an internet gateway
The next thing we will do to define our subnet as a public subnet is to run a command that will make all resources launched into this subnet receive a public IP address, This is the procedure we will skip while creating the new subnet because we don't want any resouce launched into it receive a public IP, Hence the name "Private Subnet"

    aws ec2 modify-subnet-attribute \
    --subnet-id subnet-090fa6b2ea82b72b8 \
    --map-public-ip-on-launch

If you do not recieve any error response then you are good to go

next we need to create an internet gateway, attach it to our vpc and add a route to the IGW on our route table. by doing this, every resources launched into the subnet will be able to communicate with the internet

to create and intenet gateway run the command beelow

    aws ec2 create-internet-gateway

![Imgur](https://imgur.com/wgdg80T.jpg)

Take note of the JSON response you will need the information from it

Next is to attach the IGW to our VPC, to do this run the command below

    aws ec2 attach-internet-gateway \
    --vpc-id "vpc-05c375ae9942ef1b0" \
    --internet-gateway-id "igw-09384282e4ac54524" \
    --region us-east-1

If you receive no error reesponse then, you are good to go.

![Imgur](https://imgur.com/nvCnZtn.jpg)


Next, is to create a new route table, associate the route table to our public subnet and create a route to the newly attached IGW in the route table so that our resouces can be routed to the internet

to create a new route table, run the command below

    aws ec2 create-route-table \
    --vpc-id vpc-05c375ae9942ef1b0

![Imgur](https://imgur.com/IQXB1g6.jpg)

Take note of the JSON response you will need the information from it

Next, to associate our public subnet to the route table run the command below

    aws ec2 associate-route-table \
    --route-table-id rtb-0e1f598771b875295 \
    --subnet-id subnet-090fa6b2ea82b72b8

![Imgur](https://imgur.com/YPqZW34.jpg)

This command below creates a route for our newly created  route table. The route matches all IPv4 traffic (0.0.0.0/0) and routes it to the internet gateway we created few minutes ago. If the command succeeds, the json ouput "True" is returned.

    aws ec2 create-route \
    --route-table-id rtb-0e1f598771b875295 \ 
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id igw-09384282e4ac54524 \

![Imgur](https://imgur.com/4PQnIRM.jpg)


> ## Create a security group and add a SSH access from anywhere

A security group is a firewall that operates on the instance level. A security group determines which traffic are allowed and not allowed into ourinstances. 

Security groups are stateful that is if an incoming traffic from a source is allowed then the outgoing traffic from that source is allowed too not minding whether the outbound rules allows that traffic source or not.

Inbound rules are the rules defined to allow incoming traffic access into our insances and outbound rules are rules defined to allow outgoing traffic from our instances

By default, a SG denys all incoming traffic and allows all outgoing traffic from that instance. This means that whenever we create a secuirity group we have to add an inbound rule to allow traffic sources of interest (eg SSH,HTtp,HTTPS,TCP e.t.c.) access into our instance.

In this step we will create a security group into our VPC and add an inbound rule to allow SS access into our instance so that admin tasks can be performed ion our machine.

To create a security group in or vpc, run the command below

    aws ec2 create-security-group \
    --group-name Test_Security_Group \
    --description "Security group for my test VPC" \
    --vpc-id vpc-05c375ae9942ef1b0 \

![Imgur](https://imgur.com/3ynYHwE.jpg)
![Imgur](https://imgur.com/TfR0tso.jpg)


Next we will create a rule to allow SSH access from anywhere through that security group into our instance

    aws ec2 authorize-security-group-ingress \
    --group-id sg-053bc3acda81a7f2f \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0 \

![Imgur](https://imgur.com/uImm3vy.jpg)

> ## Lauch an instance into your subnet 
Next step is to launch an instance into our VPC, specify the public subnet and attach it to the security group we created earlier

to do this, run the command below

    aws ec2 run-instances \
    --image-id ami-03ededff12e34e59e \
    --instance-type t2.micro \
    --subnet-id subnet-090fa6b2ea82b72b8 \
    --security-group-ids sg-053bc3acda81a7f2f \
    --key-name lanre-key-n-virginia \

![Imgur](https://imgur.com/AtdSFZX.jpg)


If we Check the management console we will confirm that all cammand ran were correct and the resources and componeents cereated are in their right places and order.

![Imgur](https://imgur.com/EXf0VVV.jpg)


> ## Clean Up 
now we need to perform cleanup operations 
    
    * terminate instance 
    * Delete Security group
    * Delete Subnets
    * Delete route table
    * Detach IGW and delete 
    * Delete VPC

to terminate the instance running run the command below

    aws ec2 terminate-instances \
    --instance-ids i-03a789f06489e7371 

[Imgur](https://imgur.com/iKSwfd7.jpg)

### To delete security group run the command below

    aws ec2 delete-security-group \
    --group-id sg-053bc3acda81a7f2f \

If you recieve no error response, then the operation was succesful 


### To delete public subnet run the command below

    aws ec2 delete-subnet \
    --subnet-id subnet-090fa6b2ea82b72b8 \

If you recieve no error response, then the operation was succesfull

### To delete Private subnet run the command below

    aws ec2 delete-subnet \
    --subnet-id subnet-08677ae8a4ea5e9d9 \
If you recieve no error response, then the operation was succesfull

### To delete Route table run the command below

    aws ec2 delete-route-table \
    --route-table-id rtb-0e1f598771b875295 

If you recieve no error response, then the operation was succesfull

### To detach and delete the internet gateway run the commands below one after the other 

    TO DETACH
    aws ec2 detach-internet-gateway \
    --internet-gateway-id igw-09384282e4ac54524 \
    --vpc-id vpc-05c375ae9942ef1b0 

If you recieve no error response, then the operation was succesfull    

![Imgur](https://imgur.com/c31UwC1.jpg)
    
    TO DELETE 
     aws ec2 delete-internet-gateway \
    --internet-gateway-id igw-09384282e4ac54524 \
 
 If you recieve no error response, then the operation was succesfull


### To delete VPC run the command below

    aws ec2 delete-vpc \
    --vpc-id vpc-05c375ae9942ef1b0

 If you recieve no error response, then the operation was succesfull


 THANK YOU FOR FOLLOWING.