#  Working with EBS

Tasks:
1. Create an Amazon EBS volume
2. Attach and mount your volume to an EC2 instance
3. Create a snapshot of your volume
4. Create a new volume from your snapshot
5. Attach and mount the new volume to your EC2 instance


Guide:

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-volumes.html

https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-attaching-volume.html


# Notes From LAB Task

> ## 1. Create an Amazon EBS volume
An Amazon EBS volume is a durable, block-level storage device that you can attach to your instances. After you attach a volume to an instance, you can use it as you would use a physical hard drive

We will attempt to do this via the management console and to do this we will navigate to the ec2 console, on the left pane, click on volume

![Imgur](https://imgur.com/EUpcL47.jpg)

Next click on the create volume Icon 

![Imgur](https://imgur.com/HUZOcNM.jpg)

Next thing to do is to specify the type and size of volume to create. We want to create a general purpose SSD with 10gib size aj=nd 1000 IOPS in us-east-1a availability zone

![Imgur](https://imgur.com/owcGsmC.jpg)

![Imgur](https://imgur.com/XJoR9Cl.jpg)


Then our volume is creted
> ## 2. Attach and mount your volume to an EC2 instance

A volume is not utilized until it is attached to an instance hence we ned to  attac this volume to a new instance. 

Please note that this instance must have been launched in the same availabilty zone in which the volume residees.

To attach the volume, go to the volumes page and click on actions, a dropdown menu will appear and you will be able perorm some actions on your volume. Click on "attach volume"

![Imgur](https://imgur.com/jmDzW6J.jpg)

Then chose the instance to which you wish to attach your volume and click attach

![Imgur](https://imgur.com/5Q68Px9.jpg)

you should get a succes message on your dashboard like this

    "Successfully attached volume vol-04359ad9f75e02066 to instance i-068379b77f62581ef."


> ## 3. Create a snapshot of your volume
EBS Snapshots are a point-in-time copy of your data, and can be used to enable disaster recovery, migrate data across regions and accounts, and improve backup compliance

we want to create a snapshot from the volume attached to our instance

To do this, go to the volume console, click on the volume id and you will be directed to a page where you will get to see all details of the volume. Go to the top right click on actions then click on create snapshot

![Imgur](https://imgur.com/c6WjvmK.jpg)

![Imgur](https://imgur.com/eA83dTn.jpg)

Then make sure to add a description of the snapshot for easy identification in later times. then click on create 

![Imgur](https://imgur.com/hAWEz7o.jpg)

> ## 4. Create a new volume from your snapshot

It is possible to create a new volume from the snapshot we just created and attach this new volume to any instance we want to

to do this, go to the snapshots tab on the left pane

![Imgur](https://imgur.com/TfnZJbz.jpg)

Then click on the snapshot ID and you will be taken to a page where all details of the snapshot will be shown

![Imgur](https://imgur.com/dj5J4fR.jpg)

Once you enter the page go to the top right corner and click on actions and then create volume from snapshot

![Imgur](https://imgur.com/77Ppsp7.jpg)

Fill in the details of your volume as you need. but be mindful of the cost. 

![Imgur](https://imgur.com/L5JQycw.jpg)

![Imgur](https://imgur.com/hK9bbui.jpg)

Then go back to the volume page to name the volume for easy identification

> ## 5. Attach and mount the new volume to your EC2 instance

Next we will attach the new volume to any instance we will be needing it. for this lab we will create another instance so that we can compare later on. 

once a new instance has been created go back to the volume pae and attach it to the new instance.


![Imgur](https://imgur.com/QHDa2Jm.jpg)

Now, the volume has been attached to the new instance and resources stored in the volume are now accesible to the new instance

> ## Lastly, make sure to perform clean up operations to avoid beign billed for resources that are not in use.

> ## THANK YOU FOR FOLLOWING!!
