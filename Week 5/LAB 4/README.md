# Working with Auto scaling groups

Tasks:
1. Create a launch template
2. Create a single-instance Auto Scaling group
3. Verify your Auto Scaling group
4. Terminate an instance in your Auto Scaling group
5. Clean up



Guide:
https://docs.aws.amazon.com/autoscaling/ec2/userguide/GettingStartedTutorial.html


> # Notes From LAB Task

AWS Auto Scaling monitors your applications and automatically adjusts capacity to maintain steady, predictable performance at the lowest possible cost. 

Using AWS Auto Scaling, it's easy to setup application scaling for multiple resources across multiple services in minutes. in simple words, Auto Scaling is used for automatic scaling up and scaling down.

>## 1. Create a launch template



Open the Amazon EC2 console.

On the navigation bar at the top of the screen, select an AWS Region. The Amazon EC2 Auto Scaling resources that you create are tied to the Region that you specify.

![Imgur](https://imgur.com/MGZwmTk.jpg)

In the left navigation pane, choose Launch Templates, and then choose Create launch template.

![Imgur](https://imgur.com/cVdhHlM.jpg)
![Imgur](https://imgur.com/UDiKWdu.jpg)

* For Launch template name, enter *demo-template-for-auto-scaling*. (or any name that you deem fit)

* Under Auto Scaling guidance, select the check box.

![Imgur](https://imgur.com/PfoPJZa.jpg)

* For Application and OS Images (Amazon Machine Image), choose a version of Amazon Linux 2 (HVM) from the Quick Start list. The AMI serves as a basic configuration template for your instances.

* For Instance type, choose a hardware configuration that is compatible with the AMI that you specified.



![Imgur](https://imgur.com/px37jMf.jpg)


* (Optional) For Key pair (login), choose an existing key pair. You use key pairs to connect to an Amazon EC2 instance with SSH. Connecting to an instance is not included as part of this lab task. Therefore, you don't need to specify a key pair unless you intend to connect to your instance using SSH.

* For Network settings, Security groups, choose a security group in the same VPC that you plan to use as the VPC for your Auto Scaling group. If you don't specify a security group, your instance is automatically associated with the default security group for the VPC.

* You can leave Advanced network configuration empty. Leaving the setting empty creates a primary network interface with IP addresses that we select for your instance based on the subnet to which the network interface is established. If instead you choose to configure a network interface, the security group must be a part of it.

![Imgur](https://imgur.com/imQ61Ef.jpg)

* Choose *Create launch template*.



![Imgur](https://imgur.com/VQ5yTlG.jpg)

> ## 2. Create a single-instance Auto Scaling group



Now use Amazon EC2 Auto Scaling to create an Auto Scaling group and add the launch template to the group. 

Use the following procedure to continue where you left off after creating a launch template 

* To create an Auto Scaling group, click on the launch template ID you just created, click on actions and select "create autoscaling group"

![Imgur](https://imgur.com/cUZosvV.jpg)


* For Auto Scaling group name, enter *demo-asg*.

Choose Next.

![Imgur](https://imgur.com/aCVn0al.jpg)


* The Choose instance launch options page appears, allowing you to choose the VPC network settings you want the Auto Scaling group to use and giving you options for launching On-Demand and Spot Instances (if you chose a launch template).


![Imgur](https://imgur.com/h6vAzt5.jpg)
* In the Network section, keep VPC set to the default VPC for your chosen AWS Region, or select your own VPC. The default VPC is automatically configured to provide internet connectivity to your instance. This VPC includes a public subnet in each Availability Zone in the Region.

* For Availability Zones and subnets, choose a subnet from each Availability Zone that you want to include. Use subnets in multiple Availability Zones for high availability. For more information, see Considerations when choosing VPC subnets.

![Imgur](https://imgur.com/LeqwShw.jpg)




Keep the rest of the defaults for this lab and choose Skip to review.

![Imgur](https://imgur.com/LeqwShw.jpg)


Note
The initial size of the group is determined by its desired capacity. The default value is 1 instance.

On the Review page, review the information for the group, and then choose Create

![Imgur](https://imgur.com/ZsauIbx.jpg)



> ## 3. Verify your Auto Scaling group

Now that you have created an Auto Scaling group, you are ready to verify that the group has launched an EC2 instance.


To verify that your Auto Scaling group has launched an EC2 instance

* Open the Auto Scaling groups page in the Amazon EC2 console.


* Select the check box next to the Auto Scaling group that you just created.

* A split pane opens up in the bottom of the Auto Scaling groups page. The first tab available is the Details tab, showing information about the Auto Scaling group.

![Imgur](https://imgur.com/7DOVQZM.jpg)


* Choose the second tab, Activity. Under Activity history, you can view the progress of activities that are associated with the Auto Scaling group. The Status column shows the current status of your instance. While your instance is launching, the status column shows PreInService. The status changes to Successful after the instance is launched. You can also use the refresh button to see the current status of your instance.

![Imgur](https://imgur.com/HCGRj9c.jpg)


On the Instance management tab, under Instances, you can view the status of the instance.

Verify that your instance launched successfully. It takes a short time for an instance to launch.

![Imgur](https://imgur.com/q1XLUYu.jpg)


The Lifecycle column shows the state of your instance. Initially, your instance is in the Pending state. After an instance is ready to receive traffic, its state is InService.


![Imgur](https://imgur.com/LDWAAln.jpg)


The Health status column shows the result of the EC2 instance health check on your instance.

![Imgur](https://imgur.com/Dea5yYa.jpg)

> ## 4. Terminate an instance in your Auto Scaling group

Use these steps to learn more about how Amazon EC2 Auto Scaling works, specifically, how it launches new instances when necessary. The minimum size for the Auto Scaling group that you created in this tutorial is one instance. Therefore, if you terminate that running instance, Amazon EC2 Auto Scaling must launch a new instance to replace it.

* Open the Auto Scaling groups page in the Amazon EC2 console.

* Select the check box next to your Auto Scaling group.

* On the Instance management tab, under Instances, select the ID of the instance.

![Imgur](https://imgur.com/LlnP9fX.jpg)


* This takes you to the Instances page in the Amazon EC2 console, where you can terminate the instance.

* Choose Instance State, Terminate. When prompted for confirmation, choose Yes, Terminate.

![Imgur](https://imgur.com/e2TdjrZ.jpg)


On the navigation pane, under Auto Scaling, choose Auto Scaling Groups. Select your Auto Scaling group and choose the Activity tab.

![Imgur](https://imgur.com/mYWGQW5.jpg)



The default cooldown for the Auto Scaling group is 300 seconds (5 minutes), so it takes about 5 minutes until you see the scaling activity. In the activity history, when the scaling activity starts, you see an entry for the termination of the first instance and an entry for the launch of a new instance.


![Imgur](https://imgur.com/e5Ydw9H.jpg)



On the Instance management tab, the Instances section shows the new instance only.


![Imgur](https://imgur.com/gi7Qeir.jpg)


On the navigation pane, under Instances, choose Instances. This page shows both the terminated instance and the new running instance.

![Imgur](https://imgur.com/7ezdTj1.jpg)

> ## 5. Clean up

### To delete your Auto Scaling group

* Open the Auto Scaling groups page in the Amazon EC2 console.

* Select your Auto Scaling group (demo-asg).

* Choose Delete. When prompted for confirmation, choose Delete.

![Imgur](https://imgur.com/utPiSLX.jpg)


A loading icon in the Name column indicates that the Auto Scaling group is being deleted. When the deletion has occurred, the Desired, Min, and Max columns show 0 instances for the Auto Scaling group. It takes a few minutes to terminate the instance and delete the group. Refresh the list to see the current state.




OR

You can delete the ASG using AWS CLI by running the command below

    aws autoscaling delete-auto-scaling-group \
    --auto-scaling-group-name demo-asg


### To delete your launch template

Open the Amazon EC2 console.

* On the navigation pane, under Instances, choose Launch Templates.

* Select your launch template (demo-template-for-auto-scaling).

* Choose Actions, Delete template. When prompted for confirmation, choose Delete template.

![Imgur](https://imgur.com/6nD7ER2.jpg)

OR

You can delete the Launch template using theLI by running the command below

    aws ec2 delete-launch-template \
    --launch-template-id lt-03ed245b302c25054 \
    --region us-east-1

You should get a JSON response like this

    {
    "LaunchTemplate": {
        "LaunchTemplateId": "lt-03ed245b302c25054",
        "LaunchTemplateName": "demo-template-for-auto-scaling",
        "CreateTime": "2022-05-02T19:36:22+00:00",
        "CreatedBy": "arn:aws:iam::220572702244:user/Son-of-order",
        "DefaultVersionNumber": 1,
        "LatestVersionNumber": 1
    }
    }


>## THANK YOU FOR FOLLOWING
