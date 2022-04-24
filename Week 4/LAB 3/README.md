# Working with placement Groups using AWS CLI

Tasks:
1. Create a placement group
2. Describe a placement group
3. Launch instances in a placement group
4. Describe instances in a placement group
5. Change the placement group for an instance
6. Delete a placement group


Guide:
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/placement-groups.html#using-placement-groups



# Notes from LAB Task

> ## Create a placement group
A placement group is a logical grouping of instances within a single Availability Zone. Using placement groups enables applications to participate in a low-latency, 10 Gbps network. Placement groups are recommended for applications that benefit from low network latency, high network throughput, or both

To create a placement roup using the CLI, run the command below

    aws ec2 create-placement-group --group-name test-cluster --strategy cluster

MAke sure to include the type/strategy of the placement group you are creating

![Imgur](https://imgur.com/vQfKpIT.jpg)


> ## Tag a placement group

By tagging our placement groups, we want to be able to identify the placement group for when we launch instances into it.

To do this, run the command below

    aws ec2 create-tags \
    --resources pg-04b4d4bb7587f774f \
    --tags 'Key="[Name]",Value=Test-placement-group'

If you receive no erroer response, then the operation was sucesful

If you want to check the tags of your placement groups run the command below

    aws ec2 describe-tags \
    --filters Name=resource-type,Values=placement-group

You should get a response where all tags of your group is shown.

![Imgur](https://imgur.com/S8BYUwm.jpg)


> ## Launch instances in a placement group

It is important to note that Placement groups help us to launch a bunch of EC2 instances close to each other physically within the same AZ. Being close physically and within the same AZ helps it take advantage of high-speed connectivity to provide low latency, high throughput access.

You can launch an instance into a placement group if the placement group rules and limitations are met.

To launch instances into a placement group using the AWS CLI, Use the run-instances command and specify the placement group name using the --placement "GroupName = test-cluster" parameter. In this lab, the placement group is named test-cluster

    aws ec2 run-instances \
    --placement "GroupName = test-cluster" \
    --image-id ami-03ededff12e34e59e \
    --instance-type c5.large \
    --key-name lanre-key-n-virginia

You should get a JSON response like this

    {
    "Groups": [],
    "Instances": [
        {
            "AmiLaunchIndex": 0,
            "ImageId": "ami-03ededff12e34e59e",
            "InstanceId": "i-0827166579ced7484",
            "InstanceType": "c5.large",
            "KeyName": "lanre-key-n-virginia",
            "LaunchTime": "2022-04-24T20:39:48+00:00",
            "Monitoring": {
                "State": "disabled"
            },
            "Placement": {
                "AvailabilityZone": "us-east-1b",
                "GroupName": "",
                "Tenancy": "default"
            },
            "PrivateDnsName": "ip-172-31-91-11.ec2.internal",
            "PrivateIpAddress": "172.31.91.11",
            "ProductCodes": [],
            "PublicDnsName": "",
            "State": {
                "Code": 0,
                "Name": "pending"
            },
            "StateTransitionReason": "",
            "SubnetId": "subnet-00cb17467724d35f0",
            "VpcId": "vpc-0753daf7540da0384",
            "Architecture": "x86_64",
            "BlockDeviceMappings": [],
            "ClientToken": "47ec8345-d020-4b0a-b205-bcd7df74bb1d",
            "EbsOptimized": false,
            "EnaSupport": true,
            "Hypervisor": "xen",
            "NetworkInterfaces": [
                {
                    "Attachment": {
                        "AttachTime": "2022-04-24T20:39:48+00:00",
                        "AttachmentId": "eni-attach-03a4aaa036da5c707",
                        "DeleteOnTermination": true,
                        "DeviceIndex": 0,
                        "Status": "attaching",
    -- More  --

NOTE: Not all instance types are supported to be launched into a placement group with cluster strategy and this was why we launched a c5.large instance type.

The following instance types are supported:

* Current generation instances, except for burstable performance instances (for example, T2) and Mac1 instances.

* The following previous generation instances: A1, C3, cc2.8xlarge, cr1.8xlarge, G2, hs1.8xlarge, I2, and R3.

ALSO NOTE: A cluster placement group can't span multiple Availability Zones.


> ## Describe instances in a placement group
 
 when We want to know the instance launched into a particular placement group, we run the aws ec2 describe instances as shown below

    aws ec2 describe-instances \
    --filters "Name = placement-group-name, Values = test-cluster"

You should get this JSON response

    {
    "Reservations": [
        {
            "Groups": [],
            "Instances": [
                {
                    "AmiLaunchIndex": 0,
                    "ImageId": "ami-03ededff12e34e59e",
                    "InstanceId": "i-0827166579ced7484",
                    "InstanceType": "c5.large",
                    "KeyName": "lanre-key-n-virginia",
                    "LaunchTime": "2022-04-24T20:24:43+00:00",
                    "Monitoring": {
                        "State": "disabled"
                    },
                    "Placement": {
                        "AvailabilityZone": "us-east-1b",
                        "GroupName": "test-cluster",
                        "Tenancy": "default"
                    .
                    .
                    .
                    .
                ~More~
> ## 5. Change the placement group for an instance

For this task, we will create a new instance and move into our placement group "test-cluster" 

After the instance has been created, move the instance to the placement group

To move the instance, you need to;

* Stop the instance using the stop-instances command.

* Use the modify-instance-placement command and specify the name of the placement group to which to move the instance.

to stop the instance running run the command below
    aws ec2 stop-instances --instance-ids i-06dd7d0ee2dd93ffe

You should get the JSON response below

    {
    "StoppingInstances": [
        {
            "CurrentState": {
                "Code": 64,
                "Name": "stopping"
            },
            "InstanceId": "i-06dd7d0ee2dd93ffe",
            "PreviousState": {
                "Code": 16,
                "Name": "running"
            }
        }
    ]
}

Next, move the instance into the group using the command below

    aws ec2 modify-instance-placement \
    --instance-id i-06dd7d0ee2dd93ffe \
    --group-name test-cluster

You should get a response like this if the operation was sucesful
    
    {
    "Return": true
    }

Now, if we run the ec2 desciribe command gain we would see that our new instance has been moved succesfully

    aws ec2 describe-instances \
    --filters "Name = placement-group-name, Values = test-cluster"


You should get a JSON response like this: 

    {
    "Reservations": [
        {
            "Groups": [],
            "Instances": [
                {
                    "AmiLaunchIndex": 0,
                    "ImageId": "ami-03ededff12e34e59e",
                    "InstanceId": "i-0827166579ced7484",
                    "InstanceType": "c5.large",
                    "KeyName": "lanre-key-n-virginia",
                    "LaunchTime": "2022-04-24T20:24:43+00:00",
                    "Monitoring": {
                        "State": "disabled"
                    },
                    "Placement": {
                        "AvailabilityZone": "us-east-1b",
                        "GroupName": "test-cluster",
                        "Tenancy": "default"
                    },
                    .
                    .
                    .
     {
            "Groups": [],
            "Instances": [
                {
                    "AmiLaunchIndex": 0,
                    "ImageId": "ami-03ededff12e34e59e",
                    "InstanceId": "i-06dd7d0ee2dd93ffe",
                    "InstanceType": "c5.large",
                    "KeyName": "lanre-key-n-virginia",
                    "LaunchTime": "2022-04-24T20:39:48+00:00",
                    "Monitoring": {
                        "State": "disabled"
                    },
                    "Placement": {
                        "AvailabilityZone": "us-east-1b",
                        "GroupName": "test-cluster",
                        "Tenancy": "default"

                    .
                    .
                    .

NOTE: THE THREE DOTS I PLACED IN THE MIDDLE AND THE END OF THE JSON OUTPUT DENOTES TRUNCATIONS MEANING THERE ARE OTHER DETAILS LISTED IN THE JSON BUT I CHOSE TO CUT THEM OUT BECAUSE WE DO NOT NEED THEM FOR THIS LAB TASK.

> ## 6. Delete a placement group

Before we delete a placement group, you must first terminate the instances launched into it. to delete the two instances, run the commands below and replace the instance id with that of your instance

    aws ec2 terminate-instances --instance-ids i-06dd7d0ee2dd93ffe

You should get a JSON response like this

    {
    "TerminatingInstances": [
        {
            "CurrentState": {
                "Code": 48,
                "Name": "terminated"
            },
            "InstanceId": "i-06dd7d0ee2dd93ffe",
            "PreviousState": {
                "Code": 80,
                "Name": "stopped"
            }
        }
    ]
}

Terminate the second instance

    aws ec2 terminate-instances --instance-ids i-0827166579ced7484


You should gtet a JSON response like this

    {
    "TerminatingInstances": [
        {
            "CurrentState": {
                "Code": 32,
                "Name": "shutting-down"
            },
            "InstanceId": "i-0827166579ced7484",
            "PreviousState": {
                "Code": 16,
                "Name": "running"
            }
        }
    ]
}

Lastly, Use the delete-placement-group command and specify the placement group name to delete the placement group. 

    aws ec2 delete-placement-group --group-name test-cluster

If you do not get an error response then the operation was succesfull.


> ##  THANK YOU FOR FOLLOWING.

