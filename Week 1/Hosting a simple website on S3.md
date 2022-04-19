# Hosting a simple website on S3

Amazon S3 can be used to host simple static websites and we'll do a walk through on how tto get this done.

Below is the link to a site I hosted written in htmml, CSS and Java script

http://starlite-fitness.s3-website-us-east-1.amazonaws.com/?

First, you need to create a bucket, we have gone through how to create a bucket in the previous lab so we'll skip that 

When the bucket is created, enter the bucket console and then go to the properties tab. Scroll to the last option in the tab, you will see static web hosting, clck on edit and enter the panel

![Imgur](https://imgur.com/ApNmGhD.jpg)

Once you enter there you wll see an option to enable web hosting, click on it and configure the other details

![Imgur](https://imgur.com/mZwRwe2.jpg)

Make sure to put in the exact name of your index document(home page) into the index name pane and the exact name of your error page file into the error name pane. then click create

![Imgur](https://imgur.com/zAUBGeX.jpg)
![Imgur](https://imgur.com/XT8D8q0.jpg)

Next is to upload your all websites documents directly into the bucket 

![Imgur](https://imgur.com/XtEIkPU.jpg)

Lastly, Go to the properties page and scroll down to static web hosting and copy the bucket endpoint. Go ahead and paste into your browser and our site is up.

![Imgur](https://imgur.com/Tq4m9yW.jpg)

    
![Imgur](https://imgur.com/uhKg43V.jpg)





THANK YOU FOR FOLLOWING.