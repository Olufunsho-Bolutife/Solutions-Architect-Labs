# Task 2: Create Amazon S3 bucket with bucket policies and life cycle management

1. Launch AWS Console
2. Create a S3 bucket with enforced ownership
3. Create a single lifecycle policy
4. Create a single bucket policy
5. Delete all policies
6. Delete S3 bucket



For guide, kindly visit

https://docs.aws.amazon.com/cli/latest/reference/s3api/create-bucket.html

https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html

https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html


#  Notes From Practice with lab 2

## 1. Lanuch AWS console
Here, we won't be using the AWS CLI to acces AWS resources instead we will be using the GUI (in order words the management console).

The first thing to do is to get to the Log in page, log in as an IAM user. (the reason is because Security best practices clearly advices users to perform tests and day to day activities with IAM user accounts instead of the root account)

![img](https://i.imgur.com/BRdYVZi.jpg)
 
Input accoun ID/ Alias, Email, and passowrd for the IAM user account just as shown below.

![Imgur](https://imgur.com/hTFIEQL.jpg)

Wiait a few seconds and you are in the terminal

![Imgur](https://imgur.com/B8UobCR.jpg)

Next is to navigate to the Amazon S3 pane and to do that, click on "SERVICES" on the top left corner of the console and type "s3" into the search box. The S3 service box wll appear, click on it and you will be directed into the S3 console.

![Imgur](https://imgur.com/XaqitF3.jpg)

## 2. Create a bucket
These next set of steps wil show how to create a sample bucket in s3 with enforced ownership(i.e the writing and reading from the bucket will be strictly done by the bucket creator except a bucket policy is written to allow any other pricipal)

First, Click "Create bucket"

![Imgur](https://imgur.com/a8HnMxm.jpg)

The next thing is to name the bucket and the  region in which you want the bucket to reside. Note that S3 is a global service and as such yoyur bucket name must be unique and must not have been used by any other aws user at any point in time)

![Imgur](https://imgur.com/eTy2hSK.jpg)

Next is to enforce bucket ownership (Object ownership determines who can specify access to objects) 
there are two options to choose from. ACL disablead and ACL enabled.

If you chose disable ACL then you are stating that All objects in the bucket are owned by this account and access to this bucket and its objects is specified using only policies.

If you choose enable ACL then you are stating that Objects in the bucket can be owned by other AWS accounts and access to th bucket and its objects can be specified using ACLs.

ACL means Access Control Lists.

To restrict and enforce the bucket ownership to our account alone, we will disable ACL.

![Imgur](https://imgur.com/2RRwSye.jpg)

Next is to chose whether you want to block public accesss to your bucket and its objects. Depending on the use of the bucket we can decide to block public acees or not...

if for example we are using the bucket to save media files of our websites then we have to make it accessible to the public so that visitors of our websites can have access to those files...

but in another scenairo where the bucket is beign used to store backup files from our database instance or log files from our application for analysis with Athena then we do not need to enable public access since it's only the bucket owner and other Priviledged IAM users that will be accessing the buckets.

![Imgur](https://imgur.com/H2PSlJ1.jpg)

Next is to define whether you want to enable versioning on the bucket to kep track of previous versions of objects in the buckets. You also have the ability to chose whter you want to enable encryption om the bucket for the purpose of securing the objects uploaded into the bucket

![Imgur](https://imgur.com/IjdPq1W.jpg)

Then click on create bucket
![Imgur](https://imgur.com/uH9kpkN.jpg)

![Imgur](https://imgur.com/b1dnDsR.jpg)

## 3. Create a single Lifecycle policy
Lifecycle rules are used to define actions you want Amazon S3 to take during an object's lifetime such as transitioning objects to another storage class, archiving them, or deleting them after a specified period of time.

Let's take a scnenairo where our bucket will be used to store media files (RESULT Sheets of students) of a Secondary school's website. 

 As the months go by, the number of get requests to the results sheets will reduce and as such we will need to move the objects into other classes of storage so that we won't be billed more than our active usage. hence, the need for a lifecycle rule

By default, all objects are placed into the  s3 Standard  storage class which give 11 9s of durabilty, 4 9s of availability and "MS" retrival speed which is what we need for our result sheets to be easily accesed and readily available to our students.

We are anticipating frequent access to the results within the first month of the result upload but after the first month, access to the results will be reduced drastically because majority of the students would have checked and downloaded their results by then. So while creating the bucket we will create a lifecycle rule to move the objects(result sheet) into S3 Standard Infrequent access(IA) one month from the upload date. 

Subsequntly, access to the results will reduce to about once in 3 months so we will inlcude in our lifecycle rule that after 4 months from the day our objects were created, they should be moved to Amazon Glacier Instant retriever because by this time, the results for the next term would have been uploaded and traffic will now be focused on the new results. 

Lastly, we will include in our lifecycle rule that after one year from the upload date, all objects should be moved to Amzon Glacier deep archive where we will be chared the least because the sets of objects are no longer beign accessed they are just there for te sakes of records and compliance policies. 

So let's create our life cyccle policy. 

First we enter into the bucket created and navigate to the"management" option 

![Imgur](https://imgur.com/wJ74KaS.jpg)

once you are in, click on create life cycle rule

![Imgur](https://imgur.com/pJj95MW.jpg)

name the rule and choose to apply the rule to all objects in the bucket

![Imgur](https://imgur.com/q4aF88M.jpg)

Next is to define the transition rules and parametes, we ask s3 to move the objects(Result sheets) into S3 Standard IA after 30 days from creation/upload  and then 3 months from that time which is 120 days from day of creation/upload, the objects should be moved into Amazon Glacier Instant retrieval and then after a year (i.e 365 days after creation)the objects should be moved to amzon Glacier deep archive for records. 


![Imgur](https://imgur.com/dpivEUN.jpg)

Next is to review the rule and click on create rule

![Imgur](https://imgur.com/X6KDxQK.jpg)

## 4. Create a bucket policy
Bucket policies, written in JSON, provides access to the objects stored in the bucket. Bucket policies don't apply to objects owned by other accounts.
We will define a policy to allow read-only access to the objects in our bucket since our bucket is used to store result sheets which students need to access.

First, we need to enter the bucket once agin and then click on the permissions tab 

![Imgur](https://imgur.com/xEblKdL.jpg)

Go to bucket policy and click on edit 

![Imgur](https://imgur.com/wM06DQF.jpg)

Once you enter the policy console, click on policy genrator on the top right corner of the console.

![Imgur](https://imgur.com/FBh7ngR.jpg)

When the generator opens up, state the parameters of our policy and give permisiion to all users on the internet to read the objects in our bucket 

![Imgur](https://imgur.com/agdaH3O.jpg)

![Imgur](https://imgur.com/0Eax3Hj.jpg)

as seen above the action we stated were s3:GetObject and  s3:GetObjectVersion

Below is the policy generated. Then we copy it and go paste into the bucket policy editor in the console of our bucket

![Imgur](https://imgur.com/P97AEqh.jpg)

![Imgur](https://imgur.com/6HueEFf.jpg)
Then click on save to save the policy

![Imgur](https://imgur.com/bghC93w.jpg)

 then go to the objects in the bucket to test the policy, before we added the policy, the objects were not accesible to anyone but after the bucket policy was added, the obeject url became accesible

  ![Imgur](https://imgur.com/apwqehv.jpg)

 ![Imgur](https://imgur.com/k7bGpzL.jpg)


## 5. Delete all policies

First we diable the lifecycle rule by heading to the management tab, click on life cycle rules, then we will see the rule we created , click on it and go to action then disable it.

![Imgur](https://imgur.com/PkcOIV6.jpg)

Next, Delete the rule byu clicking delete and this pane will appear 

![Imgur](https://imgur.com/te660dy.jpg)

to delete the Bucket policy, head down to the permision tab and scroll back to bucket policy, and click delete at the top right of the pane.

![Imgur](https://imgur.com/1sOhzyb.jpg)

![Imgur](https://imgur.com/8Oe4ft2.jpg)

## 6.  Delete Bucket
To delete the bucket, go back to your bucket console, select the bucket, and click on delete.

![Imgur](https://imgur.com/k9UI3v3.jpg)


THANK YOU FOR FOLLOWING.