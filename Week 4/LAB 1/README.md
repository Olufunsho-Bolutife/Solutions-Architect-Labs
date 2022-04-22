# Working with EC2 instance using the AWS CLI / Programmable access

Tasks:

1. Using your default vpc, find the public subnet
2. Create a security group
3. Launch an instance with a web server with termination protection enabled
4. Monitor Your EC2 instance; view the types of metrics that are collected for an EC2 instance
5. Modify the security group that your web server is using to allow HTTP access
6. Resize your Amazon EC2 instance to scale


Guide
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/terminating-instances.html


https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-resize.html

# Notes From the LAB Work

> ## 1. Using your default vpc, find the public subnet

For this Lab, we are going to lanucn an instance in our default vpc and subnet so our first task is to find a public subnet in our default vpc.

To do this, we are going to run the ec2-describe-subnets command. We will specify our default vpc id and the command will list out all subnets available in that vpc


    aws ec2 describe-subnets \
    --filters "Name=vpc-id,Values=vpc-0753daf7540da0384"

![Imgur](https://imgur.com/qF8WlQP.jpg)

Now all we need to do is to get the subnet ID of one of te subnets and  keep it aside becasue we will launch our ec2 instance into that subnet. Another thing to note is that that subnet must allow resouces launched into it to have a public ip address i.e ("MapPublicIponLaunch" value must be "true")

Subnet ID - subnet-0f7a91856fd5fd11b 


> ## 2. Create a security group

To create a security group, we neeed to run the command 

     aws ec2 create-security-group \
    --group-name Test_Security_Group \
    --description "Security group for my test VPC" \
    --vpc-id vpc-0753daf7540da0384

If the command returns the security group ID as output, then the operation was succesful.

![Imgur](https://imgur.com/oNCIurq.jpg)

> ## 3. Launch an instance with a web server with termination protection enabled

to do this, we will run the command below and specify the default subnet ID we copied before now, the security group we created just now

     aws ec2 run-instances \
    --image-id ami-03ededff12e34e59e \
    --instance-type t2.micro \
    --subnet-id subnet-0f7a91856fd5fd11b \
    --security-group-ids sg-0dc1375667a36313f \
    --key-name lanre-key-n-virginia


![Imgur](https://imgur.com/3gLk8AV.jpg)

Next we will SSH into the instance, apply neccesary updatesand install Apache server since the instance is going to be utilized as a web server.

In perevious Labs we have shown how to ssh into our instances. Kindly make reference to previous labs on how to ssh into the instance. Also note that you must have created an inbound rule to allows ssh traffic into the instance, to do that run the command below

    aws ec2 authorize-security-group-ingress \
    --group-id sg-0dc1375667a36313f \
    --protocol tcp \
    --port 22 \
    --cidr 0.0.0.0/0    

![Imgur](https://imgur.com/z3umyl0.jpg)


To update all packages run the command below

    sudo yum update -y


Next, we will install the apache server and to do that, run the command below

    sudo yum install httpd -y


Next, we will start the server so that it can begin to run. In addition ot that we will run a command to enable the server to start automatically after a reboot so that we don't have to alwas come to start the server after after every boot.

    sudo systemctl start httpd
    sudo systemctl enable httpd

 
 You can also check the status of the server to know if it is running already and when it started running. To do this run this command

    sudo systemctl status httpd

![Imgur](https://imgur.com/fqs6GqQ.jpg)

Next is to test the server through our browser. to do this you copy the public IPv4 address allocated to the instance on launch and then paste in your browser, you should be directed to the apache test page.  This is proof that your web server is up and running and you can now upload your code and run it through the server

![Imgur](https://imgur.com/f9PdE0n.jpg)
![Imgur](https://imgur.com/2nwJ79a.jpg)

> ## 4. Monitor Your EC2 instance; view the types of metrics that are collected for an EC2 instance

Here, we want to monitor some important metric of our instances such as CPU utilization, Totatl bytes in , Total bytes out and may other important metrics which the instance generates for the puprose of monitiring the state and status of the server.

To do this we will just simply run the ec2 describe instances command 

    aws ec2 describe-instances \
    --instance-ids i-09c6271dd337c318e

![Imgur](https://imgur.com/MQlw2Ya.jpg)

![Imgur](https://imgur.com/dBa8jOA.jpg)

![Imgur](https://imgur.com/8Rf6upE.jpg)

![Imgur](https://imgur.com/BeOLsJG.jpg)

We can also monitor some of our instance metrics from the mangement console such as CPU utilization, Totatl bytes in , Total bytes out. To do this, Log into the console, navgate to the ec2 console and click on the just created instance and scroll

![Imgur](https://imgur.com/0YSGReL.jpg)

![Imgur](https://imgur.com/KqbF60E.jpg)

![Imgur](https://imgur.com/1Mhq4AK.jpg)

> ## 5. Modify the security group that your web server is using to allow HTTP access

We need to do this sp that our server will be able to recive and process incoming http traffic since our web server serves traffic through http protocol

To do thid, we run the command below

    aws ec2 authorize-security-group-ingress \
    --group-id sg-0dc1375667a36313f \
    --protocol tcp \
    --port 80 \
    --cidr 0.0.0.0/0

![Imgur](https://imgur.com/oW9Dqa9.jpg)

> ## 6. Resize your Amazon EC2 instance to scale

Assuming after we launched our instance and we are now recieving traffic from the internet and we realise that the traffic we are recieving is beyond the capacity of the instance, we will have to resize the instance so that it can scale to handle our traffic excellently.

To do this, first you have to stop the instance, then move on to change the size of the instance.

To stop the instance, run the command

    aws ec2 stop-instances --instance-ids i-09c6271dd337c318e

![Imgur](https://imgur.com/fOPt2IQ.jpg)

Then to resize the instance from a "t2.micro" to a "t2.nano", run the command below

    aws ec2 modify-instance-attribute \
    --instance-id i-09c6271dd337c318e \
    --instance-type "{\"Value\": \"t2.nano\"}"
If you do not recieve an errror report then you are good to go

![Imgur](https://imgur.com/u5OuRk4.jpg)

Now, start the instance again and we are set with a new instance type and size.

    aws ec2 start-instances \
    --instance-ids i-09c6271dd337c318e

![Imgur](https://imgur.com/BMsFslJ.jpg)

> ## Lastly, don't forget to perform clean up operations so tha you won't incur unnecessary costs on your free tier account.

Terminate the instance;

    aws ec2 terminate-instances \
    --instance-ids i-09c6271dd337c318e


Delete the Security group;

    aws ec2 delete-security-group \
    --group-id sg-0dc1375667a36313f

If you get no error response then the operation was succesful.

     
> ## THANK YOU FOR FOLLOWING.

