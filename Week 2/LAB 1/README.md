This task will get you acquainted on how to deploy VPC 

Task 1: Deploy VPC using Management console
1. Launch the AWS Management Console
2. Launch a VPC with one public and one private subnets.
3. Create two route table and associate  each one to each subnets
4. Attach an internet gateway to the VPC and a NAT gateway to the private subnet.
5. Lauch a Linux instance into this VPC and attach the subnet
6. Test the network connectivity
7. Perform clean up actions







For guide, you are check the links below:

https://docs.aws.amazon.com/cli/latest/reference/ec2/

https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-vpcs.html

# Notes from Experimenting with this Lab Tasks

## 1. Launch the AWS Management Console
Open your browser and navigate to AWS console home. At the top left corner enter keyword "VPC" in the search box and enter into the AWS VPC console

![Imgur](https://imgur.com/emfHXST.jpg) 

![Imgur](https://imgur.com/ftz8skr.jpg)


## 2. Launch a VPC with one public and one private subnets.
Click on "your VPC" and you will be directed into a page where all VPC in your default are displayed then at the top rigth corner click on create VPC
    
![Imgur](https://imgur.com/fyzVNuu.jpg) 
    
When the create page opens up give your vpc a name, specify the region where you waant it to reside (VPCs are region specific)

Next is to specify your Ipv4 CIDR range (a range of public and private IP addresses which resources in your vpc can take on)

![Imgur](https://imgur.com/biaX0q8.jpg) 

after this is done click on Create. The VPC is on and running.

![Imgur](https://imgur.com/DpUWJIM.jpg)

![Imgur](https://imgur.com/ud6tzQh.jpg)

![Imgur](https://imgur.com/vDJeQ0S.jpg)

![Imgur](https://imgur.com/UAZkhde.jpg)


Next is to create 2 subnets into our vpc one public and one private subnet which is where we can directly launch our aws resources into. To do this, click on the subnets tab by the left of the vpc management page. click create and edit your subnet parameters

![Imgur](https://imgur.com/MVvV72D.jpg)

![Imgur](https://imgur.com/LEGmG6s.jpg)

Input the name of your subnet, the AZ which you want it to reside, the IPv4 CIDR (Note: this must be smaller than the one you used while ceating the VPC because the number of assignable IP addresss in a subnet cannot be greater than the total number of asssignable Ip addresses in the VPC) then click on create

![Imgur](https://imgur.com/PbcKkIo.jpg)

![Imgur](https://imgur.com/Y3I7fPH.jpg)

To create the private subnet go through the same steps, the only difference is that while filling the CIDR for This new subnet, you must make sure that it does not overlap the CIDr of the previous subnet created. 

![Imgur](https://imgur.com/giivyyU.jpg)

![Imgur](https://imgur.com/aFbpr59.jpg)

For our VPC the number of assignable IP addresses we can have is 65,536 and for each of our subnets, we have 256 assignable IP addresses

Next is to enable Auto assign IPv4 IP addreses so that resources in our public subnet can have public IP addresses assigned to them. to do this follow the steps in the screenshots below

![Imgur](https://imgur.com/6NiAqNu.jpg)

![Imgur](https://imgur.com/U1kUhBe.jpg)


## 3. Create two route table and associate  each one to each subnets
Next is to create a route table ansd associate each to our subnets.
A route table is a vpc resource that determines where traffic is directed/routed to.

To create a route table, go to the left panel and click on route tables. Next is to click on create route table. The next set of pictures shows how to create a route table

![Imgur](https://imgur.com/ahQd8GB.jpg)

![Imgur](https://imgur.com/NAo3pGo.jpg)

![Imgur](https://imgur.com/e4uL5z0.jpg)

After the route tables have been created, we need to associate it to our public subnet since we intend to use it to define the direction of traffic for our public subnet, to do this, follow the steps as shown in the images below

![Imgur](https://imgur.com/qD1MABI.jpg)

![Imgur](https://imgur.com/ByIA24f.jpg)

![Imgur](https://imgur.com/DAJVldp.jpg)

To create the route table which we will associate with our private subnet, follow the exact same step as you did while creating the previos route table the only difference is that you will name this new route table as "Private_rt" so that you can identify easily. 

![Imgur](https://imgur.com/ar8ffcw.jpg)

You will aslo follow the same steps to associate the route table to our private subnet

![Imgur](https://imgur.com/m0n0aDW.jpg)

## 4. Attach an internet gateway to the VPC and a NAT gateway to the private subnet.

An internet gateway is a very Important VPC component that allows resources in our public subnets to connect and have access to the internet.

We have to create an internet gateway, attach it to our VPC and update our public route table by creating a route to the internet gateway so that the resources in the public subnet can communicate with the internet. The following Pictures give sstep by step instructions on how to get this done

![Imgur](https://imgur.com/lIejVnm.jpg)
 
![Imgur](https://imgur.com/7MsWNqL.jpg)

![Imgur](https://imgur.com/JDyX9Xk.jpg)

![Imgur](https://imgur.com/lPczZIr.jpg)

![Imgur](https://imgur.com/KBNv7x5.jpg)

![Imgur](https://imgur.com/rA7Px8c.jpg)

![Imgur](https://imgur.com/f0hDfkW.jpg)

![Imgur](https://imgur.com/WygUL5p.jpg)

![Imgur](https://imgur.com/VvaI8RP.jpg)

![Imgur](https://imgur.com/vs0D9X9.jpg)

![Imgur](https://imgur.com/HDE1RJd.jpg)

![Imgur](https://imgur.com/HDRXZcJ.jpg)


Because we have defined our second subnet as a private subnet, it is impossible for the resources in that subnet to communicate with the internet. but in the case where the instances in our private subnets needs to connect to the internet for security updates and patches we need to create a way through which those resources will communicate with the internet Hence, the need for a NAT gateway. 

A NAT gaeway is an AWS mangaed VPC component that allows the resources in our private subnets have access to the internet incase of any operations that needs to be performed. 

First, we create a Nat Gateway in our public subnet because we need the Gateway to have internet access then we will update our private route table and add a route to the NAT gateway, By doing this the resources in our private subnets can now have internet access to perform specific operations.

Note: he fact that a NAT gateway has been associated with our private subnet does not mean users on the internet will be able to connect to our instance or send requests to it because our NAT gateway will only allow request traffic originating from the private subnet resources and the corresponding response traffic to those requests.

Below are the steps to create a NAT gateway in a public subnet and associate it to our private subnet.

![Imgur](https://imgur.com/GuzFdtC.jpg)

![Imgur](https://imgur.com/ZcFDfu8.jpg)

![Imgur](https://imgur.com/prUpy9j.jpg)

![Imgur](https://imgur.com/1eXzXLb.jpg)

![Imgur](https://imgur.com/Y7Fzhv0.jpg)


## 5. Lauch a Linux instance into this VPC and attach the subnet
Now, we will launch  Linux instances into our VPC. we will launch one in our public subnet and another in the  private subnet. 

To launch our EC2 instance in public subnet, we will choose an Amazon Linux 2 AMI, choose a t2.micro instance type, choose default storage, configure a new security group to receive internet traffic (in case of a web server) and SSH traffic inclusive so that we can ssh into the instance to perform administration and other configuration activities in the instance.

The following images show us step by step instructions on how to launch the instance

![Imgur](https://imgur.com/3R07N51.jpg)

![Imgur](https://imgur.com/BMSXYmc.jpg)

![Imgur](https://imgur.com/tY4aPSS.jpg)

![Imgur](https://imgur.com/YV8Us2R.jpg)

![Imgur](https://imgur.com/2PRFjHt.jpg)

![Imgur](https://imgur.com/OsfUCIT.jpg)

![Imgur](https://imgur.com/4bRe04d.jpg)

![Imgur](https://imgur.com/qWWPI1a.jpg)

![Imgur](https://imgur.com/zMuDieN.jpg)

![Imgur](https://imgur.com/q5R7AUF.jpg)

![Imgur](https://imgur.com/iXXYbJl.jpg)

![Imgur](https://imgur.com/UYpnAiQ.jpg)


To launch our private instance, we will follow the same pattern but note that in the configuration/details page, you have to state that the instance will be launched into the private subnet. Then we configure a new security group to receive SSH traffic only from the security group of our public instance because we will use that public instance to log into our private instance.

![Imgur](https://imgur.com/O9sxnAw.jpg)

![Imgur](https://imgur.com/5rm7tZe.jpg)

![Imgur](https://imgur.com/oxEScuj.jpg)


## 6. Test the network connectivity
Next we will test the network connectivity of our instances
    
We will start from our public instance. what we will do is to SSH into the instance and attempt to ping a site. We will also apply update on the instance and install apache server and open the test page on our browser through the assigned public IP address/DNS of the instance.

First, go to your instances console click on the public instance and click on the connect tab.

![Imgur](https://imgur.com/HbXpp1g.jpg)

when you enter the console, navigate to the SSH tab

![Imgur](https://imgur.com/KhHOlbw.jpg)

Read the instructions on that page carefully, Open your terminal, navigate to the directory where your pem file is located. once you are there, run the chmod command 

    chmod 400 demo_key_pair.pem

![Imgur](https://imgur.com/0cWxN4M.jpg)

Next is to ssh into the instance. run the command as seen in the ssh connect console

    ssh -i "demo_key_pair.pem" ec2-user@ec2-3-210-201-171.compute-1.amazonaws.com

![Imgur](https://imgur.com/3gI9HeO.jpg)

Now, you are inside the instance, first thing to do to test your connectivity to the internet, run a ping command to any website of your choice

    ping google.com

![Imgur](https://imgur.com/XZZL781.jpg)

Next Let's install apache server on the instance. run the command

    sudo yum install httpd -y

Next we need to start the serber and enablethe server to come up automatically upon instance reboot

    sudo systemctl start httpd
    sudo systemctl enable httpd

We can check the status of the server by running the systemctl status command

    sudo systemctl status httpd

![Imgur](https://imgur.com/yn9JgaR.jpg)

Next, copy the Public IP address of the instance on the console and paste in your browser, you should see the Apache server test page.

![Imgur](https://imgur.com/xmBlFjC.jpg)

![Imgur](https://imgur.com/05OHGv1.jpg)


To test the connectivity of the private instance,
SSH into the instance from the public subnet, and then perform a ping on the instance. If we recieve a response from the site then it has ben confirmed that the instance is connected to the internet.

![Imgur](https://imgur.com/GTmGVE3.jpg)

![Imgur](https://imgur.com/Ij3iHmO.jpg)



## 7. Perform clean up actions
The Last thing to do is to perfform cleanup operations so thatt we wont be charged for resources we are not using

        * Delete the Instances
        * Detach the Internet gateway
        * Delete the internet gateway
        * Dissociate the Elastic IP adddress created when creating the  Nat gateway
        * Delete the ELastic Ip address 
        * Delete the Nat Gateway
        * Dissociate the subnets from the route table
        * Delete the route tables
        * Delete the Subnets
        * Delete The VPC







THANK YOU FOR FOLLOWING.