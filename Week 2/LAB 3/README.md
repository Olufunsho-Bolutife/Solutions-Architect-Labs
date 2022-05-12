Task :  Create a dual Stack VPC and subnets using AWS CLI

Step:
1. Launch a non-default VPC
2. Create two subnets 
3. Create an internet gateway to one of the subnets with route table 
4. Configure an egress-only private subnet
5. Modify the IPv6 addressing behavior of the subnets
6. Lauch an instance into your public subnet
7. Launch an instance into your private subnet
8. Perform clean up operations. 


https://docs.aws.amazon.com/vpc/latest/userguide/vpc-subnets-commands-example.html

https://docs.aws.amazon.com/vpc/latest/userguide/vpc-subnets-commands-example-ipv6.html

https://docs.aws.amazon.com/vpc/index.html


# Notes from the lab work
----------





> ## Launch a non-default VPC
in the previous lab, we demonstrated how to launch a non default vpc in our default region. for the purpose of this demonstration, we will repeat it.

To launch a non default vpc, run the command below

    aws ec2 create-vpc \
    --cidr-block 10.0.0.0/16

![Imgur](https://imgur.com/WotWSg7.jpg)

Make sure you take note of all the information being given in the JSON responses. The JSON contains important information like the ID of our VPC components which we will be referencing as we move on

> ## Create two subnets 

We will review how to create subnets once again in this lab demonstration.

To create the public subnet, run the command below

    To create the public subnet, run the command below
    
    aws ec2 create-subnet \
    --vpc-id vpc-03c3feb2d14bc61f6 \
    --cidr-block 10.0.1.0/24 \

![Imgur](https://imgur.com/KvuXFKZ.jpg)

Take note of the JSON response you will need the informations from it


Next, we will configure the subnet to be able to allocate public IPv4 addresses to the resources launched into that . To do this, we run the command below

    aws ec2 modify-subnet-attribute \
    --subnet-id subnet-0c998351ca3175085 \
    --map-public-ip-on-launch

If you do not recieve any error response then you are good to go


Next, we need to create the private subnet. What to do is to follow the same steps we used in creatingthe oublic subnet but we won't run the modify-subnet command for public IPv4 adddress allocation. Just run the command below

    aws ec2 create-subnet \
    --vpc-id vpc-03c3feb2d14bc61f6 \
    --cidr-block 10.0.2.0/24 \

    NOTE: Your subnet must not have Overlapping CIDR range with that of the public subnet

![Imgur](https://imgur.com/A8T3R9N.jpg)


>##  Create an internet gateway to one of the subnets with route table 
Next we will create a internet gateway which will serve as an entry and exit point from our VPC to the internet.

to Create the IW, run the command below

    aws ec2 create-internet-gateway

![Imgur](https://imgur.com/ayxe1H6.jpg)

Next, we need to attch that Internet gateway to our VPC, we do that by running the command below

    aws ec2 attach-internet-gateway \
    --vpc-id "vpc-03c3feb2d14bc61f6" \
    --internet-gateway-id "igw-0e21213147af49c52" \
    --region us-east-1 \

If you receive no error reesponse then, you are good to go.

Next, we need to create a route table which is the vpc component that determines where traffic is routed to in and from our VPC

to Create our route table, run the command below

    aws ec2 create-route-table \
    --vpc-id vpc-03c3feb2d14bc61f6 \

![Imgur](https://imgur.com/4YBD2IZ.jpg)

Take note of the JSON response you will need the information from it

Next is to associate our public subnet to the route table run the command below

    aws ec2 associate-route-table \
    --route-table-id rtb-0336403b90b1842e8 \
    --subnet-id subnet-0c998351ca3175085 \

![Imgur](https://imgur.com/aeqC4Pc.jpg)

The command below creates a route for our newly created  route table. The route matches all IPv4 traffic (0.0.0.0/0) and routes it to the internet gateway we created few minutes ago. If the command succeeds, the json ouput "True" is returned.

    aws ec2 create-route \
    --route-table-id rtb-0336403b90b1842e8 \
    --destination-cidr-block 0.0.0.0/0 \
    --gateway-id igw-0e21213147af49c52 \

![Imgur](https://imgur.com/lncSEXn.jpg)

Next we need to create a route table which we will associate with our private subnet. We will run these commands one after the other 

    TO CREATE ROUTE TABLE
    aws ec2 create-route-table \
    --vpc-id vpc-03c3feb2d14bc61f6 \

![Imgur](https://imgur.com/Hc3YpWW.jpg)

    TO ASSOCIATE SUBNET
    aws ec2 associate-route-table \
    --route-table-id rtb-0ace70ce9bd3a6f13 \
    --subnet-id subnet-08415dd8ed13c760b \

![Imgur](https://imgur.com/eZZsL9s.jpg)

> ## Configure an egress-only private subnet
An eggress-only internet gateway is a VPC component that allows outbound traffic to the internet from a vpc subnet over IPv6 addresss only to do this. but recall that whil we wre creating the vpc, we did not specify the need for the providsioning of an IPv6 CIDR block into our VPC and therefore we won't be able to create an egress only IGW nor process traffic through it because there isn't  an IPv6 CIDR associated with that vpc so the first thing to do is to associate an amazon provided IPV6 CIDR to our vpc, then we can now create the egress only IGW and create a route to it in the route table of our private subnet.

To associate an Amazon-provided IPv6 CIDR block with our VPC, run the command below

    aws ec2 associate-vpc-cidr-block \
    --amazon-provided-ipv6-cidr-block \
    --ipv6-cidr-block-network-border-group us-east-1  \
    --vpc-id vpc-03c3feb2d14bc61f6 \

![Imgur](https://imgur.com/SRJ0M9p.jpg)

Next, we create an egress IGW and create a route to it in our private route table. We will do that by running theses commands one after the other 

    TO CREATE EGREES-ONLY-IGW
    aws ec2 create-egress-only-internet-gateway \
    --vpc-id vpc-03c3feb2d14bc61f6 \

![Imgur](https://imgur.com/jolp0uE.jpg)

    TO CREATE A EGRESS-ONLY ROUTE 
    aws ec2 create-route \
    --route-table-id rtb-0ace70ce9bd3a6f13 \
    --destination-ipv6-cidr-block ::/0 \
    --egress-only-internet-gateway-id eigw-03ef1e9613bd34125 \
if the you receive a true response then you are good to go

![Imgur](https://imgur.com/UCBXZYt.jpg)


> ## Lauch an instance into your public subnet
We have created our public subnet, and created an internet gateway to route traffic to the internet, we will proceed to launch an instance into that subnet

But befoee we do thuis, we need to create a security group which will serve as a firewall to our instances and determine the kind of traffic that comes in oand out of our instance.

To create the security group, run the command

    aws ec2 create-security-group \
    --group-name Test_Security_Group \
    --description "Security group for my test VPC" \
    --vpc-id vpc-03c3feb2d14bc61f6 \

![Imgur](https://imgur.com/fJ2p5X0.jpg)

Next we will create a rule to allow SSH access from anywhere through that security group into our instance

    aws ec2 authorize-security-group-ingress \
    --group-id sg-02fb203bb35fec6b0 \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0 \

![Imgur](https://imgur.com/0xNufLE.jpg)

Next we will lauch the instances and specify these components we have creaed before now

      aws ec2 run-instances \
    --image-id ami-03ededff12e34e59e \
    --instance-type t2.micro \
    --subnet-id subnet-0c998351ca3175085 \
    --security-group-ids sg-02fb203bb35fec6b0 \
    --key-name lanre-key-n-virginia \

![Imgur](https://imgur.com/pkduH51.jpg)

To test the connectivity of the instance to the internet, we will ssh into the instance and perform a ping command

![Imgur](https://imgur.com/xJZEJcL.jpg)


> ## Launch an instance into your private subnet
To do this, we need to run the command below just as we did for the previous instance but this time around, we are going to launch it in the private subnet

    aws ec2 run-instances \
    --image-id ami-03ededff12e34e59e \
    --instance-type t2.micro \
    --subnet-id subnet-08415dd8ed13c760b \
    --security-group-ids sg-02fb203bb35fec6b0 \
    --key-name lanre-key-n-virginia \

![Imgur](https://imgur.com/mxPZRw5.jpg)

> ## Perform clean up operations. 

now we need to perform cleanup operations 
    
    * terminate instance 
    * Delete Security group
    * Delete Subnets
    * Delete route table
    * Detach IGW and delete
    * Delete the Egress Only IGW
    * Delete VPC

to terminate the instances running run the commands below

    aws ec2 terminate-instances \
    --instance-ids i-08b1e048974274e9a 
    
    AND

    aws ec2 terminate-instances \
    --instance-ids i-0e5172b717a83f689
    

### To delete security group run the command below

    aws ec2 delete-security-group \
    --group-id sg-02fb203bb35fec6b0

If you recieve no error response, then the operation was succesful 


### To delete public subnet run the command below

    aws ec2 delete-subnet \
    --subnet-id subnet-0c998351ca3175085 \

If you recieve no error response, then the operation was succesfull

### To delete Private subnet run the command below

    aws ec2 delete-subnet \
    --subnet-id subnet-08415dd8ed13c760b \
If you recieve no error response, then the operation was succesfull

### To delete Route tables run the commands below

    aws ec2 delete-route-table \
    --route-table-id rtb-0336403b90b1842e8 

    AND

    aws ec2 delete-route-table \
    --route-table-id rtb-0ace70ce9bd3a6f13 


If you recieve no error response, then the operation was succesfull

### To detach and delete the internet gateway run the commands below one after the other 

    TO DETACH
    aws ec2 detach-internet-gateway \
    --internet-gateway-id igw-0e21213147af49c52 \
    --vpc-id vpc-03c3feb2d14bc61f6 

If you recieve no error response, then the operation was succesfull    

![Imgur](https://imgur.com/c31UwC1.jpg)
    
    TO DELETE 
     aws ec2 delete-internet-gateway \
    --internet-gateway-id igw-0e21213147af49c52 \
 
 If you recieve no error response, then the operation was succesfull

### To delete the Egress only internet gateway run the command below

    aws ec2 delete-egress-only-internet-gateway \
    --egress-only-internet-gateway-id eigw-

image

### To delete VPC run the command below

    aws ec2 delete-vpc \
    --vpc-id vpc-03c3feb2d14bc61f6

 If you recieve no error response, then the operation was succesfull


 THANK YOU FOR FOLLOWING. 
 