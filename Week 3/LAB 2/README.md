Creating a User Pool

Tasks: 
1. Go to the Amazon Cognito console. 
2. Choose Manage User Pools.
3. Choose Create a user pool.
4. Enter a name for your user pool and choose Review defaults to save the name.
5. On the Review page, choose Create pool.




Guide:

https://docs.aws.amazon.com/cognito/latest/developerguide/cognito-scenarios.html

https://docs.aws.amazon.com/cognito/latest/developerguide/tutorial-create-identity-pool.html




# Notes From Lab Tasks

> ##  Go to the Amazon Cognito console. 

![Imgur](https://imgur.com/KwzfZxc.jpg)

> ##  Choose Manage User Pools.

![Imgur](https://imgur.com/1HFjauF.jpg)

> ##  Choose Create a user pool.

![Imgur](https://imgur.com/AbFdZKF.jpg)


> ##  Enter a name for your user pool and choose Review defaults to save the name.

![Imgur](https://imgur.com/vbQ7zhi.jpg)


> ##  On the Review page, choose Create pool.
![Imgur](https://imgur.com/5RtRDIK.jpg)

![Imgur](https://imgur.com/yf2HJqF.jpg)

> ## Once the user pool has been created, you will be directed to this page that shos you all information about your pool.

![Imgur](https://imgur.com/fhZ0ldk.jpg)

![Imgur](https://imgur.com/wRxYNJk.jpg)


To create a userpool with the AWS CLI, run the command below

    aws cognito-idp create-user-pool --pool-name MyUserPool



THANK YOU FOR FOLLOWING.