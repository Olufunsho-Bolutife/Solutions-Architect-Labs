This Week lab will focus on getting you acquainted with Amazon S3 using the aws cli

# Task 1: Create a S3 bucket using AWS CLI

1. Lauch AWS cloud shell
2. Create a S3 bucket 
3. Create a folder in your S3 bucket
4. Upload objects to your S3 bucket
5. List the object in your S3 bucket
6. Download Object from your S3 bucket
7. Delete object in your s3 bucket
8. Delete your S3 bucket





For guide, kindly visit

https://docs.aws.amazon.com/cli/latest/reference/s3api/create-bucket.html

https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html

https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html

# Learning Experience from working wiith LAB 1

### 1. Launch AWS Cloud shell
to do this the first thing to do is to log in to the aws management console an then access the AWS Cloudshell to begin writng the codes

Or

Install the AWS CLI on your windows local machine and then go to your command line or bash terminal or windows terminal, launch it, supply your AWS credentials and then you can now acces AWS through the terminal.

### 2. Create a s3 bucket
to do that all i did was to type in the command below

    aws s3 mb s3://archlanretest

    "in my case, the name of my bucket is archlanretest"

    output: make_bucket: archlanretest

    this shows that the operation was succesfull. 
    At the end of this note, screenshot of this action will be included.
![](https://i.imgur.com/x8ofG5w.jpg) *Creating a s3 bucket named archlanretest*

### 3. Create a folder in your S3 bucket
the new bucket that just been created has not folder or files in it obviously and the next step was to create a folder in the bucket for the purpose of organizing the files beign uploaded into the bucket, this was the code i ran

    aws s3 cp /c/users/user/downloads/1.pdf s3://archlanretest/demo/1.pdf

    output: upload: downloads\1.pdf to s3://archlanretest/demo/1.pdf

    In this line of code, you will discover that what i did was to upload a file into the bucket, specify the path of the bucket and include the name of the new folder i'm creeating (demo) in the destination path even though the folder does not exist and that was how i created a folder in the bucket

    when i performed the aws s3 ls command, my new folder was listed as a folder in the bucket confirming that the folder was succesfully created.
![](https://i.imgur.com/7GCsPe7.jpg) *Creating a folder inside the bucket*

### 4. Upload objects to your S3 bucket
to upload an object from my local machine into the s3 bucket i simply run the aws copy command and include the current path of the file in my pc and the s3 bucket path as the destination path. 

    aws s3 cp /c/users/user/downloads/2.pdf s3://archlanretest/demo/2.pdf
    
    Output: upload: downloads\2.pdf to s3://archlanretest/demo/2.pdf
![](https://imgur.com/lquGqFp.jpg) *Uploading a pdf file into the bucket*
### 5. List the object in your S3 bucket
To list the objects on my bucket all i need to do is to use the ls commad

    aws s3 ls s3://archlanretest/
    Output:                     PRE demo/
    aws s3 ls s3://archlanretest/demo/
    Output: 2022-04-07 01:13:43     100375 1.pdf
            2022-04-07 02:19:56     200264 2.pdf
![](https://imgur.com/Qo9xq6o.jpg) *Listing the objects in bucket archlanretest*

### 6. Download Object from your S3 bucket
to download object from the bucket all i had to do was to run the copy (cp) command again but this time the previous destination path (s3 bucket) becomes the origin path and the previous origin path become the destination path.
    
    aws s3 cp s3://archlanretest/demo/2.pdf /c/users/user/desktop/2.pdf
    output: download: s3://archlanretest/demo/2.pdf to desktop\2.pdf
![](https://imgur.com/YrbdWTz.jpg) *Downloading objects from the bucket into local machine*

### 7. Delete object in your s3 bucket
to delete objects from my bucket all i had to was to run the remove command 

    aws s3 rm s3://archlanretest/demo/1.pdf
    Output: delete: s3://archlanretest/demo/1.pdf

    aws s3 rm s3://archlanretest/demo/2.pdf
    Output: delete: s3://archlanretest/demo/2.pdf
![](https://imgur.com/qje2J6r.jpg) *Deleting objects in the bucket*

![](https://imgur.com/daxjq0p.jpg) *Deleting the folder created*

### 8. Delete your S3 bucket
To delete a bucket, all that nneds tp be done is to use the aws s3 rb command 
    
    aws s3 rb s3://archlanretest
    output: remove_bucket: archlanretest

    Note: before i ran the rb command, i did a listing command to list all the buckets i had in my default region and my bucket was listed among them but after i ram the rb command i ran the list command once again and my bucket was mnot among those listed. this shows tat the bucket has been succesfully deleted.

![](https://imgur.com/gpZf2wF.jpg) *Deleteing the s3 bucket archlanretest*

Thank you for following 



