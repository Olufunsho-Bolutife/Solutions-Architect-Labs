#  Working with AWS Cloudtrail

1. Review AWS account activity in event history
2. Create your first trail
3. View your log files



Guide:
https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-tutorial.html



> # Notes from Lab Task


CloudTrail is enabled on your AWS account when you create the account. When activity occurs in any AWS service that supports CloudTrail, that activity is recorded in a CloudTrail event along with other AWS service events in Event history. In other words, you can view, search, and download recent events in your AWS account before creating a trail, though creating a trail is important for long-term records and auditing of your AWS account activity. Unlike a trail, Event history only shows events that have occurred over the last 90 days.


> ## 1. Review AWS account activity in event history

* Sign into the management console and navigate to the clouTrail console

* Review the information in your dashboard about the most recent events that have occurred in your AWS account.

![Imgur](https://imgur.com/YmxA5mF.jpg)

* To see more information about an event, expand it.

![Imgur](https://imgur.com/Ocn92FL.jpg)



* In the navigation pane, choose Event history. You see a filtered list of events, with the most recent events showing first. The default filter for events is Read only, set to false. You can clear that filter by choosing X at the right of the filter and choose whatever filter you need

![Imgur](https://imgur.com/EWMhMbV.jpg)

* You can save event history by downloading it as a file in CSV or JSON format. Downloading your event history can take a few minutes.

![Imgur](https://imgur.com/femCs3P.jpg)

> ## 2. Create your first trail

While the events provided in Event history in the CloudTrail console are useful for reviewing recent activity, they are limited to recent activity, and they do not include all possible events that can be recorded by CloudTrail. Additionally, your view of events in the console is limited to the AWS Region where you are signed in. To create an ongoing record of activity in your AWS account that captures information for all AWS Regions, create a trail. By default, when you create a trail in the CloudTrail console, the trail logs events in all Regions. Logging events in all Regions in your account is a recommended best practice.

To set up a trail do the following;

* navigate to the CloudTrail service home page, the Trails page, or the Trails section of the Dashboard page, choose Create trail.

* In Trail name, give your trail a name, such as *demo-trail* As a best practice, use a name that quickly identifies the purpose of the trail.

* Leave default settings for AWS Organizations organization trails. This option won't be available to change unless you have accounts configured in Organizations.

* For Storage location, choose Create new S3 bucket to create a bucket. When you create a bucket, CloudTrail creates and applies the required bucket policies. Give your bucket a name, such as my-bucket-for-storing-cloudtrail-logs.

* To make it easier to find your logs, create a new folder (also known as a prefix) in an existing bucket to store your CloudTrail logs. Enter the prefix in Prefix.

![Imgur](https://imgur.com/QFCKRYp.jpg)

* Clear the check box to disable Log file SSE-KMS encryption. By default, your log files are encrypted with SSE-S3 encryption


* Leave default settings in Additional settings.

* For now, do not send logs to Amazon CloudWatch Logs.

* In Tags, add one or more custom tags (key-value pairs) to your trail. Tags can help you identify your CloudTrail trails and other resources, such as the Amazon S3 buckets that contain CloudTrail log files. For example, you could attach a tag with the name *Compliance* and the value *backups*.


![Imgur](https://imgur.com/ltkCUaV.jpg)

When you are finished creating tags, choose Next.

* On the Choose log events page, select event types to log. For this trail, keep the default, Management events. In the Management events area, choose to log both Read and Write events, if they are not already selected. Leave the check boxes for Exclude AWS KMS events and Exclude Amazon RDS Data API events empty, to log all events.

![Imgur](https://imgur.com/YGnrevG.jpg)



* Leave default settings for Data events and Insights events. This trail will not log any data or CloudTrail Insights events. Choose Next.

* On the Review and create page, review the settings you've chosen for your trail. Choose Edit for a section to go back and make changes. When you are ready to create your trail, choose Create trail.

* The Trails page shows your new trail in the table. Note that the trail is set to Multi-region trail by default, and that logging is turned on for the trail by default.


![Imgur](https://imgur.com/BlV3cX2.jpg)


> ## 3. View your log files


Within an average of about 15 minutes of creating your first trail, CloudTrail delivers the first set of log files to the Amazon S3 bucket for your trail. You can look at these files and learn about the information they contain.

* In the navigation pane, choose Trails. On the Trails page, find the name of the trail you just created (in the LAB, *demo-trail*).

* In the row for the trail, choose the value for the S3 bucket and the bucket opens up

![Imgur](https://imgur.com/XTkRy4w.jpg)

![Imgur](https://imgur.com/QiIXjO2.jpg)



* If you click on the *"CloudTrail/"* you will get to see all egions in which activities have been taking place then when you open a particular region, you will then track the year, month, and specific days in which your trails were made

![Imgur](https://imgur.com/zAscSJg.jpg)

![Imgur](https://imgur.com/8HlQGPc.jpg)

![Imgur](https://imgur.com/NcSLaf0.jpg)

![Imgur](https://imgur.com/llomtpn.jpg)

![Imgur](https://imgur.com/C3xtwel.jpg)

* The name of the files begin with your AWS account ID, and end with the extension *.gz* just as you saw in the image above

* To view these files, you can download them, unzip them, and then view them in a plain-text editor or a JSON file viewer. Some browsers also support viewing .gz and JSON files directly. We recommend using a JSON viewer, as it makes it easier to parse the information in CloudTrail log files.

* A typical cloud trail log looks like this on your browser but note, its best to view it with a JSON Editor for easy reads and unnderstanding

![Imgur](https://imgur.com/nBrVzPA.jpg)



> ## Lastly, make sure to perform clean up operations after you are done because custom trails cost some money unlike default trails recorded in "Events History"

* Delete the trail
* Empty S3 Bucket
* Delete S3 Bucket

>## THANK YOU FOR FOLLOWING