# Working with EFS

Task

1. Create your Amazon EC2 resources and launch two EC2 instance. 
2. Create your Amazon EFS file system with One Zone storage.
3. Connect to each of your Amazon EC2 instances, and mount the Amazon EFS file system using the same mount target for each instance.


Guide:
https://docs.aws.amazon.com/efs/latest/ug/gs-step-two-create-efs-resources.html



> # Notes from LAB task

Amazon EFS is a cloud-based file storage service for applications and workloads that run in the Amazon Web Services public cloud. AWS automatically deploys and manages the infrastructure for Elastic File System (EFS), which is distributed across an unlimited number of servers to avoid performance bottlenecks. 

Amazon's Elastic File System (EFS) is a scalable storage solution that can be used for general purpose workloads. An EFS can be attached to multiple Amazon Web Services (AWS) compute instances and on-premises servers, providing a common resource for applications and data storage in many different environments.

To begin working with Amazon EFS, you need to launch your compute instances and then attach the EFS to those resources. these instances then share the EFS and the EFS can be accesed by both resources. 

We will begin this Lab tutorial by creating the EFS 

> ##  Create your Amazon EFS file system with One Zone storage.

In this step, you create your Amazon EFS file system, to begin this, Open the Amazon EFS Management Console at https://console.aws.amazon.com/efs/.

Choose Create file system to open the Create file sys

![Imgur](https://imgur.com/2yPBc5O.jpg)


- Enter a Name for your file system.

- For Virtual Private Cloud (VPC), choose your VPC, or keep it set to your default VPC.

For Availability and Durability, choose one of the following:

* Regional to create a file system that uses Standard storage classes. Standard storage classes store file system data and metadata redundantly across all Availability Zones within an AWS Region. Regional offers the highest levels of availability and durability.

* One Zone to create a file system that uses One Zone storage classes. One Zone storage classes store file sytem data and metadata redundantly within a single Availability Zone which makes it less expensive than Standard storage classes.

Because EFS One Zone storage classes store data in a single AWS Availability Zone, data stored in these storage classes may be lost in the event of a disaster or other fault that affects all copies of the data within the Availability Zone, or in the event of Availability Zone destruction resulting from disasters, such as earthquakes and floods. But for this LAB, we will confiure the efs for One Zone Storage

![Imgur](https://imgur.com/p4fjZ7a.jpg)

Then click on customize to configure the EFS without the recomended settings.

Next, ensure to enable automatic backups, then Lifecycle Management – choose this lifecycle policy:

* Transition into IA set to 30 days since last access

* Transition out of IA set to On first access

Next, Choose the General Purpose performance mode and Bursting throughput mode and also enable encryption at resr

![Imgur](https://imgur.com/C76r3fA.jpg)

Next, is the network settings

create Mount targets with the following settings:

* create single mount target in the Availability Zone you specified earlier because our file systems uses One Zone storage classes,

* Located in the default subnets of the VPC you selected.

* Create a new VPC security group 

![Imgur](https://imgur.com/PJ6C56P.jpg)

skip the policy page and click next

![Imgur](https://imgur.com/UO9C88s.jpg)

![Imgur](https://imgur.com/dOrCmqO.jpg)

then click create

![Imgur](https://imgur.com/Y8bPxiH.jpg)

> ##  Create your Amazon EC2 resources and launch two EC2 instance. 

Before you can launch and connect to an Amazon EC2 instance, you need to create a key pair, unless you already have one. You can create a key pair using the Amazon EC2 console, and then you can launch your EC2 instance.

To create a key pair open the ec2 console at https://console.aws.amazon.com/ec2/. On the left tab, scroll down to keypairs, once the page opens, navigate to the top right of the screen an d click on create keypairs. once the keypair is created, download the .PEM file and continue to lauch the ec2 instance

To launch the EC2 instance navigate back to the Amazon EC2 console and 

Choose Launch Instance.

### In Step 1: Choose an Amazon Machine Image (AMI), find an Amazon Linux 2 AMI at the top of the list and choose Select.

![Imgur](https://imgur.com/zA2jO8J.jpg)

### In Step 2: Choose an Instance Type, choose Next: Configure Instance Details.

![Imgur](https://imgur.com/hk0usDq.jpg)

### In Step 3: Configure Instance Details, provide the following information:

* Leave Number of instances at two.(this is because we are going to attach the newly created efs to these two instances launched in the same AZ)


* For Network, choose the entry for the same VPC that you noted when you created your EFS

* For Subnet, choose a default subnet in the same AZ you created your EFS.

Choose Next: Add Storage.

Choose Next: Add Tags.

Name your instance and choose Next: Configure Security Group.

In Step 4: Configure Security Group, set Assign a security group to Select an new security group.

Choose Review and Launch.

Choose Launch.

Select the check box for the key pair that you created, and then choose Launch Instances.

![Imgur](https://imgur.com/57CxHHx.jpg)

![Imgur](https://imgur.com/DOgLyu4.jpg)

![Imgur](https://imgur.com/rhCpmEV.jpg)

> ## 3. Connect to each of your Amazon EC2 instances, and mount the Amazon EFS file system using the same mount target for each instance.

SSH into each of the instance so that we can mount the EFS on the both instance and test the File system. to do this click on the connect tab in the instance details pane, copy the command in that page and paste in your terminal (NB aws CLI must have been installed beforehand)

![Imgur](https://imgur.com/lICdGpd.jpg)


if the connection is succesful you should get this response in your terminal for each instance. (note the two different Ips differentiating instances EFS-1 from EFS-2)

![Imgur](https://imgur.com/4IV2vfC.jpg)


![Imgur](https://imgur.com/zPI65RN.jpg)


Next we will begin the process to mount the EFS. Run the command BELOW to install the amazon-efs-utils package.

    sudo yum install -y amazon-efs-utils

You should get a response like this 

![Imgur](https://imgur.com/fDSBgvm.jpg)

next create your EFS directory on each instance using the command below

        mkdir efs


If you do not get an error response then the process was succesful

confirm the availability of the diretory by performing the *ls* command 

        ls

 You should get a response like this on both instances

![Imgur](https://imgur.com/DPlUTPP.jpg)


before continuing to mount, we need to add an ibound rule to our EFS security group to allow NFS traffic from our EC2-EFS Security group.

![Imgur](https://imgur.com/sfwLrCr.jpg)

once that is done, you need to run a command to mount the EFS. To get this command, navigate to the EFS console and enter into the EFS created earlier and click on "attach" on the top right corner then you should see a page like this pop up. This is where the command to mount your exact EFS to your EC2 is found

![Imgur](https://imgur.com/VLNoBqe.jpg)


    sudo mount -t efs -o tls fs-096b56d31371c846f:/ efs

if you do not get any error response, then you have succesfully mounted the EFS onto both instances

NEXT, use the *cd* command to navigate into the EFS directory created earlier and then run the ll comand to check if there is any file in the directory. do this for both instances

    cd efs

you should get a response that says "total 0" for both instances because there has not been any file put into that directory

![Imgur](https://imgur.com/CNbVZ3N.jpg)


![Imgur](https://imgur.com/CVhL32S.jpg)

now we will move on to show that since the both instances share the same file system, if we create a document inside our EFS directory (where the EFS file system was mounted) in the first instance, we can view it and access it from the second instance.

so we will run the command below to create a document in the EFS through the first instance by running this command

        sudo touch hello-world.txt

When this is is succesful, run the *ll* command and you should see a response as shown below (take note of the ip address which is underlined, that's how we differentiate between both instances)

![Imgur](https://imgur.com/GoJareJ.jpg)

then we will proceed to the other instance and also perform an *ll* command and we should get the same response aswe did on the first instance because they both share the EFS file system.

    

![Imgur](https://imgur.com/IKTsl7T.jpg)

By this, we have shown that the EFS is working well and files are beign shared between the two ec2 instances because they both share the same mount target

If we go further to edit the document and write into it from any of the instances and then perform a *cat* command in the other instance, we should be able to see what we have written into the document from the first instance 

        sudo nano hello-world.txt

![Imgur](https://imgur.com/lIMwaDn.jpg)


![Imgur](https://imgur.com/rJgVJAj.jpg)

Then we will proceed to perform a *cat* commnand on the second instance, the operation is expected to return the content of our txt file which we edited on the second instance

![Imgur](https://imgur.com/wNyGw9C.jpg)

And that wraps it for this Lab.


>## Lastly, perform clean up operations

* Delete the EFS created
* Delete the Security groups created
* Delete the EC2 instances created


> ## THANK YOU FOR FOLLOWING.