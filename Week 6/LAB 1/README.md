# Working with CLoudwatch using AWS Console 

# Monitoring 

1. Enable billing alerts
2. Create a billing alarm
3. Check the alarm status
4. Edit a billing alarm
5. Delete a billing alarm


# Alarms

1. Create a CPU usage alarm
2. Create a load balancer latency alarm that sends email
3. Create a CloudWatch alarm based on a static threshold



Guide:
https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/gs_monitor_estimated_charges_with_cloudwatch.html

https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html

https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html


> # Notes Fro The Lab Task

Billing alerts  help you monitor the charges on your AWS bill. You can set up an alert to be notified automatically via e-mail when estimated charges reach a threshold that you choose.



> ## 1. Enable billing alerts

Before you can create an alarm for your estimated charges, you must enable billing alerts, so that you can monitor your estimated AWS charges and create an alarm using billing metric data. After you enable billing alerts, you cannot disable data collection, but you can delete any billing alarms that you created.

After you enable billing alerts for the first time, it takes about 15 minutes before you can view billing data and set billing alarms.

To enable monitoring of your estimated charges

* Open the AWS Billing console at https://console.aws.amazon.com/billing/.

* In the navigation pane, choose Preferences.

![Imgur](https://imgur.com/MBuaFYz.jpg)

* Select Receive Billing Alerts.

* Choose Save preferences.

![Imgur](https://imgur.com/nAsJCSj.jpg)

> ## 2. Create a billing alarm

To create a billing alarm

* Open the CloudWatch console at https://console.aws.amazon.com/cloudwatch/.

* In the navigation pane, choose Alarms, Create Alarm.

![Imgur](https://imgur.com/Lkuv4G4.jpg)

* Choose Select metric, Billing, Total Estimated Charge.

![Imgur](https://imgur.com/wb9Lohr.jpg)

![Imgur](https://imgur.com/a8NvEXl.jpg)

* Select the checkbox next to EstimatedCharges and choose Select metric

![Imgur](https://imgur.com/uYLp4hR.jpg)

* For Whenever my total AWS charges for the month exceed, specify the monetary amount (for this LAB, 500) that must be exceeded to trigger the alarm and send an email notification. Then choose Next.

![Imgur](https://imgur.com/Q2lcFmC.jpg)

![Imgur](https://imgur.com/lbANATr.jpg)

* For send a notification to, choose Create a new topic and then type a name for the new SNS topic and enter the email addresses that are to receive the notifications. Separate the email names with commas.

![Imgur](https://imgur.com/AIeKbA0.jpg)

* Name the alarm and give description

![Imgur](https://imgur.com/rpOEmve.jpg)

Choose Create Alarm.

![Imgur](https://imgur.com/X04vfcV.jpg)

> ## 3. Check the alarm status

To check the alarm status

* Open the CloudWatch console at https://console.aws.amazon.com/cloudwatch/.

* In the navigation pane, choose Alarms.

Select the check box next to the alarm. Until the subscription is confirmed, it is shown as "Pending confirmation". After the subscription is confirmed, refresh the console to show the updated status.

![Imgur](https://imgur.com/X04vfcV)


> ## 4. Edit a billing alarm

For example, you may want to increase the amount of money you spend with AWS each month from $500 to $1000. You can edit your existing billing alarm and increase the monetary amount that must be exceeded before the alarm is triggered.

To edit a billing alarm

* Open the CloudWatch console at https://console.aws.amazon.com/cloudwatch/.


* In the navigation pane, choose Alarms.

* Select the check box next to the alarm and choose Actions, Modify.

![Imgur](https://imgur.com/beyOVKa.jpg)

For Whenever my total AWS charges for the month exceed, specify the new amount that must be exceeded to trigger the alarm and send an email notification.

![Imgur](https://imgur.com/FW881jr.jpg)

Choose Save Changes.

> ## 5. Delete a billing alarm

you can delete a billing alarm when you no longer need it.

To delete a billing alarm

* Open the CloudWatch console at https://console.aws.amazon.com/cloudwatch/.

* In the navigation pane, choose Alarms.

* Select the check box next to the alarm and choose Actions, Delete.

![Imgur](https://imgur.com/dSOR6o8.jpg)

When prompted for confirmation, choose Yes, Delete.

image9_3









# Next, we want to craete a new alarm for a different metric 

1. Create a CPU usage alarm based on a static threshold that sends an email

in this part, we want to create an alarm with in which the metric to be watched is CPU Usage/utilization. We will set a threshold and configure te alarm to send email notofication when the threshold is breached

Kindly note that you must have launched an instance configueed as a web server before now so that we can stress the CPU and generate some metric.

To generate the metric
Once you create launch the instace, copy the instance Public IP and paste in a browser and refresh the page continously for about five minutes spo that we can generate quite a substantial metric.

Then move on to create the alarm

* Navigate to the cloudwatch console and go to alarms

* Click on create alarms

* a page like this will open, click on select metric

image9_4

* Click on EC2 as shown below

image9_5

* Click on per-instance metric

image9_6

* A list will then appear, check for your instannce name and the corresponding metric names to its right, scroll down until you see CPU Utilization, select it and click on select metric

image9_7

* a page like this will open up and you will see a line graph of the CPU Utilization of the instance over a period of about 5-10 minutes afetr we have stressed the CPU. leave all parameters ad default and scroll down

image9_8

* Next we choose the threshold type and set the threshold configuration. set the *"Threshold type"* to *static* and set the *"whenever CPUutilization is"* as *"greater than"* and *"Threshold"* to *0.65*. the reaso we are working with *0.65* is so that it can be closer to our current COU utilzation for the sake of comparison

image9_9

* Now  note what  happens to the graph when we added a threshold. A red line is drawn over that threshold. So whenever the Blue line (CPUutilization metric) crosses that line, an alarm is triggered.

image9_9_1

* then click next and a page like this will appear where you set configure notification via sns. leave the *"Alarm state Trigger"* as *"in alarm"*. then you can either selct an existing topic which you have created before or create a new topic and add your email as an endpoint(make sure that you have confirmed your subscription to that topic or Do it after you have created the alarm, this is because SNS will noy send messages to an endpoint except the subscription is confirmed)

image9_9_2

* leave the *"autoscaling actin"* in its default, then configure *"EC2 action"* to *stop instance* (this ensure that the instance stops whenever the metric exceeds the set threshold)

9_9_3

* Leave other configurations in their default and click next

* Next, set the alarm name and description

9_9_4

* then review and create 

* we can see the status of the alarm and the graph of the utilization at the moment

9_9_5

* now we will test the operation of the alarm

* we will stress the CPU by refreshing the instance public IP address on our browaer until it reaches the threshold like this


9_9_6

* Since we configures an ec2 action to stop the instance once that threshold is crossed, if we navigate to ec2, console, the instance will have been stopped

9_9_7


>## When done, please do well to perform clean up operations

* Delete the Alarm
* Delete SNS Topic
* Delete SNS subscription

>## THANK YOU FOR FOLLOWING