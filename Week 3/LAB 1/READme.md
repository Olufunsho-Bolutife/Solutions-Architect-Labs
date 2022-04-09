Working with IAM management console

Tasks

1. Login to your root account
2. Create an IAM group with S3 read only acess (S3 support).
3. Create an IAM group with EC2 read only acess (EC2 support)
4. Create an IAM group with EC2 full access (EC2 admin)
5. Create 3 IAM users and add to the each group above as descibe below:


| User        |  Group        | Permissions |
| :---: | :---:       | :---:       |
| user1  | EC2 support | Read-Only access to Amazon EC2|
| user2  | S3 support  | Read-Only access to Amazon S3 |
| user3  |  EC2 admin  | Full access to Amazon EC2 instances|

6. Test your design

7. Perform clean up operations


Replicate the process above for  Schulltech organization descrcibed below:


| User        |  Group        | Permissions |
| :---:       | :---:         | :---:       |
| Adminstaff  | EC2 support | Read-Only access to Amazon EC2|
| Techstaff   | S3 support  | Full  access to Amazon S3 |
| ITexpert    |   EC2/SDK admin | Full access to EC2 and SDK|
| Financialmanager | Billing control | Billing Full Access|
|Financeuser  | Billing user |Billing view access|



https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_users-self-manage-mfa-and-creds.html


