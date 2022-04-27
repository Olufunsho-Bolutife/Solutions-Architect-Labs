# Working with Elastic Load balancing

Tasks: Using AWS CLI,

1. Create an Application Load Balancer
2. Create a Network Load Balancer
3. Create a Gateway Load Balancer
4. Create a Classic Load Balancer
5. Perform clean up operations


NB: You need to launch at least two instances to do this. 

Guide
https://docs.aws.amazon.com/elasticloadbalancing/latest/userguide/load-balancer-getting-started.html


> # Notes from Lab Task

A load balancer is used to distribute incoming traffic across your EC2 instances. This enables you to increase the availability of your application. 

The load balancer also monitors the health of its registered instances and ensures that it routes traffic only to healthy instances. 

You configure your load balancer to accept incoming traffic by specifying one or more listeners, which are configured with a protocol and port number for connections from clients to the load balancer and a protocol and port number for connections from the load balancer to the instances.

Elastic Load Balancing supports four types of load balancers: Application Load Balancers, Network Load Balancers, Gateway load balancers and Classic Load Balancers. You can select a load balancer based on your application needs.

 * Application Load Balancer - Operates at the application layer (layer7) and supports HTTP and HTTPS.

* Network Load Balancer - Operates at the transport layer (layer 4)
  and supports TCP, TLS, and UDP.

* Gateway Load Balancer - Operates at the network layer (layer 3).

Note that the Classic load balancers are no longer recomended and according to AWS, they will be retiring CLB by August 15th, 2022

> ## 1. Create an Application Load Balancer
To create a load balancer

Use the create-load-balancer command to create a load balancer. You must specify two subnets that are not from the same Availability Zone.

    aws elbv2 create-load-balancer \
    --name my-application-load-balancer \
    --type application \
    --subnets subnet-0f7a91856fd5fd11b subnet-00cb17467724d35f0 \
    --security-groups sg-05c39d8c90bd95089

The output includes the Amazon Resource Name (ARN) of the load balancer, with the following JSON outputs:

    {
    "LoadBalancers": [
        {
            "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:loadbalancer/app/my-application-load-balancer/aac5ff8417ec2941",
            "DNSName": "my-application-load-balancer-542975399.us-east-1.elb.amazonaws.com",
            "CanonicalHostedZoneId": "Z35SXDOTRQ7X7K",
            "CreatedTime": "2022-04-26T16:25:01.640000+00:00",
            "LoadBalancerName": "my-application-load-balancer",
            "Scheme": "internet-facing",
            "VpcId": "vpc-0753daf7540da0384",
            "State": {
                "Code": "provisioning"
            },
            "Type": "application",
            "AvailabilityZones": [
                {
                    "ZoneName": "us-east-1b",
                    "SubnetId": "subnet-00cb17467724d35f0",
                    "LoadBalancerAddresses": []
                },
                {
                    "ZoneName": "us-east-1a",
                    "SubnetId": "subnet-0f7a91856fd5fd11b",
                    "LoadBalancerAddresses": []
                }
            ],
            "SecurityGroups": [
                "sg-05c39d8c90bd95089"
            ],
    -- More  --

Next, Use the create-target-group command to create a target group, specifying the same VPC that you used for your EC2 instances.

 The target group's IP address type determines the IP version that the load balancer will use to both communicate with, and check the health of, your backend targets. IPv4 target groups support IP and instance type targets. IPv6 targets only support IP targets.

    aws elbv2 create-target-group \
    --name my-ALB-targets \
    --protocol HTTP --port 80 \
    --vpc-id vpc-0753daf7540da0384 \
    --ip-address-type ipv4

The output includes the ARN of the target group, with this JSON response:

    {
    "TargetGroups": [
        {
            "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-ALB-targets/e28d13e189d2af50",
            "TargetGroupName": "my-ALB-targets",
            "Protocol": "HTTP",
            "Port": 80,
            "VpcId": "vpc-0753daf7540da0384",
            "HealthCheckProtocol": "HTTP",
            "HealthCheckPort": "traffic-port",
            "HealthCheckEnabled": true,
            "HealthCheckIntervalSeconds": 30,
            "HealthCheckTimeoutSeconds": 5,
            "HealthyThresholdCount": 5,
            "UnhealthyThresholdCount": 2,
            "HealthCheckPath": "/",
            "Matcher": {
                "HttpCode": "200"
            },
            "TargetType": "instance",
            "ProtocolVersion": "HTTP1",
            "IpAddressType": "ipv4"
        }
    ]
    }


Next, Use the register-targets command to register your instances with your target group:

    aws elbv2 register-targets \
    --target-group-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-ALB-targets/e28d13e189d2af50  \
    --targets Id=i-06b6a6f14670d9ae1 Id=i-0db7432aa9957310e

If you do not get an error response, the the operation was succesful


Next, Use the create-listener command to create a listener for your load balancer with a default rule that forwards requests to your target group:

    aws elbv2 create-listener \
    --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:loadbalancer/app/my-application-load-balancer/aac5ff8417ec2941 \
    --protocol HTTP --port 80  \
    --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-ALB-targets/e28d13e189d2af50

The output contains the ARN of the listener, with the following format:

    {
    "Listeners": [
        {
            "ListenerArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:listener/app/my-application-load-balancer/aac5ff8417ec2941/6b05e44fb862a62b",
            "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:loadbalancer/app/my-application-load-balancer/aac5ff8417ec2941",
            "Port": 80,
            "Protocol": "HTTP",
            "DefaultActions": [
                {
                    "Type": "forward",
                    "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-ALB-targets/e28d13e189d2af50",
                    "ForwardConfig": {
                        "TargetGroups": [
                            {
                                "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-ALB-targets/e28d13e189d2af50",
                                "Weight": 1
                            }
                        ],
                        "TargetGroupStickinessConfig": {
                            "Enabled": false
                        }
                    }
                }
            ]
        }
    ]
    }

lastly, (Optional) You can verify the health of the registered targets for your target group using this describe-target-health command:

    aws elbv2 describe-target-health --target-group-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-ALB-targets/e28d13e189d2af50

You should get a JSON response like this

      {
    "TargetHealthDescriptions": [
        {
            "Target": {
                "Id": "i-06b6a6f14670d9ae1",
                "Port": 80
            },
            "HealthCheckPort": "80",
            "TargetHealth": {
                "State": "healthy"
            }
        },
        {
            "Target": {
                "Id": "i-0db7432aa9957310e",
                "Port": 80
            },
            "HealthCheckPort": "80",
            "TargetHealth": {
                "State": "healthy"
            }
        }
    ]
    }


Next is to test the Load balancer. before now we have launched two instances in two different AZ and hosted two different websites on them running on port 80 using the http protocol.

To confirm that our ALB is up and running, the DNS of the ALB is supposed to route us to the two websites in the two instances consecutively when ever we refresh the page.

Here are screenshots to proof the operation of the ALB

![Imgur](https://imgur.com/DOfwNX4.jpg)

![Imgur](https://imgur.com/ZaZ11GM.jpg)


![Imgur](https://imgur.com/HFeUhE4.jpg)

![Imgur](https://imgur.com/khvTu3K.jpg)


> ##  Create a Network Load Balancer

Firstly, Use the create-load-balancer command to create an IPv4 load balancer, specifying a public subnet for each Availability Zone in which you launched instances. You can specify only one subnet per Availability Zone.

    aws elbv2 create-load-balancer \
    --name my-network-load-balancer \
    --type network \
    --subnets subnet-0f7a91856fd5fd11b subnet-00cb17467724d35f0 \
    --security-groups sg-05c39d8c90bd95089
  
The output includes the Amazon Resource Name (ARN) of the load balancer, with the following format:

    {
    "LoadBalancers": [
        {
            "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:loadbalancer/net/my-network-load-balancer/d77f22f8721a127c",
            "DNSName": "my-network-load-balancer-d77f22f8721a127c.elb.us-east-1.amazonaws.com",
            "CanonicalHostedZoneId": "Z26RNL4JYFTOTI",
            "CreatedTime": "2022-04-26T18:01:00.020000+00:00",
            "LoadBalancerName": "my-network-load-balancer",
            "Scheme": "internet-facing",
            "VpcId": "vpc-0753daf7540da0384",
            "State": {
                "Code": "provisioning"
            },
            "Type": "network",
            "AvailabilityZones": [
                {
                    "ZoneName": "us-east-1a",
                    "SubnetId": "subnet-0f7a91856fd5fd11b",
                    "LoadBalancerAddresses": []
                },
                {
                    "ZoneName": "us-east-1b",
                    "SubnetId": "subnet-00cb17467724d35f0",
                    "LoadBalancerAddresses": []
                }
            ],
            "IpAddressType": "ipv4"
        }
    ]
    }

Next, Use the create-target-group command to create an IPv4 target group, specifying the same VPC that you used for your EC2 instances. IPv4 target groups support IP and instance type targets. Run the command below

    aws elbv2 create-target-group \
    --name my-NLB-targets \
    --protocol TCP \
    --port 80 \
    --vpc-id vpc-0753daf7540da0384

The output includes the ARN of the target group, with this format:

    {
    "TargetGroups": [
        {
            "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-NLB-targets/00976f774c2d0e01",
            "TargetGroupName": "my-NLB-targets",
            "Protocol": "TCP",
            "Port": 80,
            "VpcId": "vpc-0753daf7540da0384",
            "HealthCheckProtocol": "TCP",
            "HealthCheckPort": "traffic-port",
            "HealthCheckEnabled": true,
            "HealthCheckIntervalSeconds": 30,
            "HealthCheckTimeoutSeconds": 10,
            "HealthyThresholdCount": 3,
            "UnhealthyThresholdCount": 3,
            "TargetType": "instance",
            "IpAddressType": "ipv4"
        }
    ]
    }

Next, Use the register-targets command to register your instances with your target group: Run the command below

    aws elbv2 register-targets \
    --target-group-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-NLB-targets/00976f774c2d0e01 \
    --targets Id=i-06b6a6f14670d9ae1 Id=i-0db7432aa9957310e


If you do not get an error response then you are good to go


Next, Use the create-listener command to create a listener for your load balancer with a default rule that forwards requests to your target group

    aws elbv2 create-listener \
    --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:loadbalancer/net/my-network-load-balancer/d77f22f8721a127c \
    --protocol TCP \
    --port 80  \
    --default-actions Type=forward,TargetGroupArn=arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-NLB-targets/00976f774c2d0e01

The output contains the ARN of the listener, with the following JSON RESPONSE:

    {
    "Listeners": [
        {
            "ListenerArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:listener/net/my-network-load-balancer/d77f22f8721a127c/f58c63a8d7ce3212",
            "LoadBalancerArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:loadbalancer/net/my-network-load-balancer/d77f22f8721a127c",
            "Port": 80,
            "Protocol": "TCP",
            "DefaultActions": [
                {
                    "Type": "forward",
                    "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-NLB-targets/00976f774c2d0e01",
                    "ForwardConfig": {
                        "TargetGroups": [
                            {
                                "TargetGroupArn": "arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-NLB-targets/00976f774c2d0e01"
                            }
                        ]
                    }
                }
            ]
        }
    ]
}

Lastly, (this is Optional) You can verify the health of the registered targets for your target group using this describe-target-health command below:

    aws elbv2 describe-target-health \
    --target-group-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-NLB-targets/00976f774c2d0e01
  
You should get a JSON response like this

      {
    "TargetHealthDescriptions": [
        {
            "Target": {
                "Id": "i-06b6a6f14670d9ae1",
                "Port": 80
            },
            "HealthCheckPort": "80",
            "TargetHealth": {
                "State": "healthy"
            }
        },
        {
            "Target": {
                "Id": "i-0db7432aa9957310e",
                "Port": 80
            },
            "HealthCheckPort": "80",
            "TargetHealth": {
                "State": "healthy"
            }
        }
    ]
    }



To confirm that our NLB is up and running, the DNS of the NLB is supposed to route us to the two websites in the previously launched instances consecutively when ever we refresh the page.

Here are screenshots to proof the operation of the NLB

![Imgur](https://imgur.com/uWK0gFl.jpg)

![Imgur](https://imgur.com/Oh54sLh.jpg)

![Imgur](https://imgur.com/8Gi8QWr.jpg)

![Imgur](https://imgur.com/RRR0xYG.jpg)


> ##  Create a Classic Load Balancer

Using a Classic Load Balancer instead of an Application Load Balancer has the following benefits:

* Support for EC2-Classic

* Support for TCP and SSL listeners

* Support for sticky sessions using application-generated cookies

Firstly, Use the following create-load-balancer command to configure the load balancer with the two listeners:

      aws elb create-load-balancer \
      --load-balancer-name my-classic-load-balancer \
      --listeners "Protocol=http,LoadBalancerPort=80,InstanceProtocol=http,InstancePort=80" \
      --availability-zones us-east-1a us-east-1b

You should get a JSON response like this

      {
    "DNSName": "my-classic-load-balancer-1880715570.us-east-1.elb.amazonaws.com"
      }


Next, you need to register your instances with the load balancer to do this, Use the register-instances-with-load-balancer command as follows:

      aws elb register-instances-with-load-balancer \
      --load-balancer-name my-classic-load-balancer \
      --instances i-06b6a6f14670d9ae1 i-0db7432aa9957310e

You should get a response like this:

    {
    "Instances": [
        {
            "InstanceId": "i-06b6a6f14670d9ae1"
        },
        {
            "InstanceId": "i-0db7432aa9957310e"
        }
    ]
    }


Next, Your load balancer is usable as soon as any one of your registered instances is in the InService state. 

To check the state of your newly registered EC2 instances, use the following describe-instance-health command:

      aws elb describe-instance-health  \
      --load-balancer-name my-classic-load-balancer \
      --instances i-06b6a6f14670d9ae1 i-0db7432aa9957310e  

you should get a JSON response like this

      {
    "InstanceStates": [
        {
            "InstanceId": "i-06b6a6f14670d9ae1",
            "State": "InService",
            "ReasonCode": "N/A",
            "Description": "N/A"
        },
        {
            "InstanceId": "i-0db7432aa9957310e",
            "State": "InService",
            "ReasonCode": "N/A",
            "Description": "N/A"
        }
    ]
    }

After the state of at least one of your instances is InService, you can test your load balancer.


To confirm that our CLB is up and running, the DNS of the CLB is supposed to route us to the two websites in the previously launched instances consecutively when ever we refresh the page.

Here are screenshots to proof the operation of the NLB

![Imgur](https://imgur.com/4Jo1PoP.jpg)

![Imgur](https://imgur.com/HbcCqk5.jpg)

![Imgur](https://imgur.com/XRMf9gg.jpg)

![Imgur](https://imgur.com/wnlznmn.jpg)

> ##  Create a Gateway Load Balancer

For this task, we do not have an application running a UDP port/GENEVE protocol and gateway balancers run on the UDP port hence we won't be able to test run our gateway load balancer even after we have created so instead, we'll give a run through on how to create a gateway load balancer.

NOTE: A Gateway Load Balancer endpoint is a VPC endpoint that provides private connectivity between virtual appliances in the service provider VPC, and application servers in the the service consumer VPC. The Gateway Load Balancer is deployed in the same VPC as that of the virtual appliances. These appliances are registered as a target group of the Gateway Load Balancer.

PRE REQUISITES

  Ensure that the service consumer VPC has at least two subnets for each Availability Zone that contains application servers. One subnet is for the Gateway Load Balancer endpoint, and the other is for the application servers.

  Ensure that the service provider VPC has at least two subnets for each Availability Zone that contains security appliance instances. One subnet is for the Gateway Load Balancer, and the other is for the instances.

  Launch at least one security appliance instance in each security appliance subnet in the service provider VPC. The security groups for these instances must allow UDP traffic on port 6081.

### STEP 1 : Create a Gateway Load Balancer and register targets
  
  Firstly, Use the create-load-balancer command to create a load balancer of type gateway. You can specify one subnet for each Availability Zone in which you launched security appliance instances

      aws elbv2 create-load-balancer \
      --name my-load-balancer \
      --type gateway \
      --subnets provider-subnet-id

The output includes the Amazon Resource Name (ARN) of the load balancer, with the format shown in the following example.

      arn:aws:elasticloadbalancing:us-east-2:123456789012:loadbalancer/gwy/my-load-balancer/1234567890123456

Next, use the create-target-group command to create a target group, specifying the service provider VPC in which you launched your instances.

      aws elbv2 create-target-group \
      --name my-targets \
      --protocol GENEVE \
      --port 6081 \
      --vpc-id provider-vpc-id

The output includes the ARN of the target group, with the following format.

      arn:aws:elasticloadbalancing:us-east-2:123456789012:targetgroup/my-targets/0123456789012345

Next, Use the register-targets command to register your instances with your target group.

      aws elbv2 register-targets \
      --target-group-arn targetgroup-arn \
      --targets Id=i-1234567890abcdef0 Id=i-0abcdef1234567890

Next, Use the create-listener command to create a listener for your load balancer with a default rule that forwards requests to your target group.

      aws elbv2 create-listener \
      --load-balancer-arn loadbalancer-arn \
      --default-actions Type=forward,TargetGroupArn=targetgroup-arn

The output contains the ARN of the listener, with the following format.

      arn:aws:elasticloadbalancing:us-east-2:123456789012:listener/gwy/my-load-balancer/1234567890123456/abc1234567890123

### Step 2: Create a Gateway Load Balancer endpoint

First, Use the create-vpc-endpoint-service-configuration command to create an endpoint service configuration using your Gateway Load Balancer.

      aws ec2 create-vpc-endpoint-service-configuration \
      --gateway-load-balancer-arns loadbalancer-arn \
      --no-acceptance-required

The output contains the service ID. (For example, vpce-svc-12345678901234567) and the service name (for example, com.amazonaws.vpce.us-east-2.vpce-svc-12345678901234567).


Next, Use the modify-vpc-endpoint-service-permissions command to allow service consumers to create an endpoint to your service. A service consumer can be an IAM user, IAM role, or AWS account. The following example adds permission for the specified AWS account.

      aws ec2 modify-vpc-endpoint-service-permissions \
      --service-id vpce-svc-12345678901234567 \
      --add-allowed-principals arn:aws:iam::123456789012:root

Next, Use the create-vpc-endpoint command to create the Gateway Load Balancer endpoint for your service.

      aws ec2 create-vpc-endpoint \
      --vpc-endpoint-type GatewayLoadBalancer \
      --service-name com.amazonaws.vpce.us-east-2.vpce-svc-12345678901234567 \
      --vpc-id consumer-vpc-id \
      --subnet-ids consumer-subnet-id


The output contains the ID of the Gateway Load Balancer endpoint (for example, vpce-01234567890abcdef).


### Step 3: Configure routing

Configure the route tables for the service consumer VPC as follows. This allows the security appliances to perform security inspection on inbound traffic that's destined for the application servers.

Firstly, Use the create-route command to add an entry to the route table for the internet gateway that routes traffic that's destined for the application servers to the Gateway Load Balancer endpoint.

      aws ec2 create-route \
      --route-table-id gateway-rtb \
      --destination-cidr-block 10.0.1.0/24 \
      --vpc-endpoint-id vpce-01234567890abcdef


Next, Use the create-route command to add an entry to the route table for the subnet with the application servers that routes all traffic from the application servers to the Gateway Load Balancer endpoint.

      aws ec2 create-route \
      --route-table-id application-rtb \
      --destination-cidr-block 0.0.0.0/0 \
      --vpc-endpoint-id vpce-01234567890abcdef


Next, Use the create-route command to add an entry to the route table for the subnet with the Gateway Load Balancer endpoint that routes all traffic that originated from the application servers to the internet gateway.

      aws ec2 create-route \
      --route-table-id endpoint-rtb \
      --destination-cidr-block 0.0.0.0/0 \
      --gateway-id igw-01234567890abcdef


lastly, Repeat for each application subnet route table in each zone.



> ##  Perform clean up operations

To Delete the Application load balancer and target group run the commands below

    aws elbv2 delete-load-balancer \
    --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:loadbalancer/app/my-application-load-balancer/aac5ff8417ec2941


    aws elbv2 delete-target-group \
    --target-group-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-ALB-targets/e28d13e189d2af50

To Delete the Network load balancer and target group run the commands below

    aws elbv2 delete-load-balancer \
    --load-balancer-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:loadbalancer/net/my-network-load-balancer/d77f22f8721a127c


    aws elbv2 delete-target-group \
    --target-group-arn arn:aws:elasticloadbalancing:us-east-1:220572702244:targetgroup/my-NLB-targets/00976f774c2d0e01


To Delete the classic load balancer run the command below

      aws elb delete-load-balancer \
      --load-balancer-name my-classic-loadbalancer

To Delete the gateway load balancer run the command below

    aws elbv2 delete-load-balancer \
    --load-balancer-arn loadbalancer-arn

Also do not Forget to delete the Instances, route tables, internet gateway, security groups and vpc endpoint connections, subnets and VPC created


>## THANK YOU FOR FOLLOWING

