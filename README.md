# Section-10

# 1.crud-app-on-single-ec2-lab

**Step 1.Launch an Instance with following settings:**
- IAM role - Ec2S3FullAccess
- Security Group - 80,22,3000 Open Port 

**Step 2.Connect the Instance and type following commands**
```sh
$ sudo su 
$ yum update -y
$ curl -sL https://rpm.nodesource.com/setup_lts.x | bash - 
$ yum install nodejs -y
$ npm install -g pm2
$ pm2 update
$ aws s3 cp s3://nodejs-curd-app-with-dynamodb-amit/Nodejs-curd-app-with-.zip .
$ unzip Nodejs-curd-app-with-dynamodb.zip
$ ls -a
$ npm install
$ npm start
```
**Step 3.Copy Public ip of instance and paste it in browser**
- e.g 13.122.218.51:3000

**Step 4.Goto Postman Tool and replace localhost:3000 with following**
- http://Public-Ip:3000/createTable
- Click on Send
  - Error:need dynamoDB access

**Step 5. Goto IAM>Roles>Ec2S3FullAccess>Attach Policy**
- Select AmazonDynamoDBFullAccess policy
- Click to attach policy

**Step 6.Go back to Step 4 and click on Send**
- Now Successful

**Step 7.Check the Table created in DynamoDB**
- Goto AWS Console>DynamoDB>Tables
  - Table is created

**Step 8.Goto Postman Tool and put the following value**
- http://Public-Ip:3000/readData

**Step 9.Goto Postman Tool and put the following value**
- http://Public-Ip:3000/insertData

**Step 10.Now Goto AWS Console>DynamoDB>Tables>Items>info**
- Check for the data inserted

**Step 11.Goto Postman Tool and put the following value**
- http://Public-Ip:3000/updateData

**Step 12.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data updated

**Step 13.Goto Postman Tool and put the following value**
- http://Public-Ip:3000/deleteTable

**Step 14.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and seee Table is deleted.

# End of Lab


# 2.crud-app-on-asg-lab

**Step 1. Goto AWS Management Console>Services>Ec2>Ec2Dashboard>Auto Scaling>Launch Configurations**

**Step 2. Click on Create Launch configuration**
- Give name - lc-cd-crud
- AMI - Copy AMI-Id from launch Instance 
- Instance type- choose t2.micro
- IAM Instance profile - EC2S3FullAccess

**Step 3. Click on Advance details>user data>As text**
- Type the following commands-
```sh
#!/bin/bash
$ yum update -y
$ yum install nmap-ncat -y
$ curl -sL https://rpm.nodesource.com/setup_lts.x | bash -
$ yum install nodejs -y
$ npm install -g pm2
$ pm2 update
$ cd /home/ec2-user
$ aws s3 cp s3://nodejs-curd-app-with-dynamodb-amit/Nodejs-curd-app-with-dynamodb.zip
$ unzip new-node-project
$ npm install
$ npm run start
$ sleep 10
$ INSTANCE_ID=$(curl -s http://169.254.169.254/latest/meta-data/instance-id)
$ aws autoscaling complete-lifecycle-action --lifecycle-action-result CONTINUE --instance-id
$ $INSTANCE_ID --lifecycle-hook-name test-hook--auto-scaling-group-name test-asg --region ap-south-1 || \
$ aws autoscaling complete-lifecycle-action --lifecycle-action-result ABANDON --instance-id
$ $INSTANCE_ID --lifecycle-hook-name test-hook --auto-scaling-group-name test-asg --region ap-south-1
```
**Step 4. Select Security Group and key pair**

**Step 5. Click Create Launch Configurations**
- Successfully created launch configuration test-lc

**Step 6. Select lc-cd-crud >Actions>Create Auto Scaling group**
- Give Auto Scaling group name - test-asg

Click on Next

**Step 7.Select Vpc and select all subnets**
- Click on Next

**Step 8.Configure advanced options**
- Load Balancing - No load Balancer
- Health checks - Select ELB 
  - Health check grace period - 120 seconds

Click on Next

**Step 9.Group size**
- Desired capacity - 1
- Minimum capacity - 1
- Maximum capacity - 1

Scaling policies - None

Click on Next

**Step 10.Add Notifications**
Click on Next

**Step 11.Add Tags**
Click on Next

**Step 12. Review and Click on Create Auto Scaling group**

**Step 13.Ec2>Auto Scaling group>Instance Management>Create lifecycle hook**
- Lifecycle hook name - test-hook
- Lifecycle transition - Instance launch
- Heartbeat timeout - 300 seconds
- Default result - ABANDON

Click on Create

**Step 14.Ec2>Auto Scaling groups>test-asg>Edit Group size**
- Desired - 1
- Minimum - 1
- Maximum - 1

Click on Update

**Step 15.Ec2>Auto Scaling Group>asg-cd>Instances**
- See the lifecycle behaviour pending>pending-wait>pending-wait:proceed>INservice

**Step 16.Click Instance and copy & paste the Public IP address in browser to see it running**
- e.g.- 13.222.125.52:3000

**Step 17.Open Postman Tool>Environment>Ec2**
- In Ec2 change current value of ec2 url - 13.222.125.52:3000

**Step 18.Now select {{url}}/create table and Click on Send**

**Step 19.Check the Table created in DynamoDB**
- Goto AWS Console>DynamoDB>Tables
  - Table is created

**Step 20.Goto Postman Tool and put the following value**
- http://{{url}}/readData

**Step 21.Goto Postman Tool and put the following value**
- http://{{url}}/insertData

**Step 22.Now Goto AWS Console>DynamoDB>Tables>Items>info**
- Check for the data inserted

**Step 23.Goto Postman Tool and put the following value**
- http://{{url}}/updateData

**Step 24.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data updated

**Step 25.Goto Postman Tool and put the following value**
- http://{{url}}/deleteData

**Step 26.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data deleted

**Step 27.Goto Postman Tool and put the following value**
- http://{{url}}/deleteTable

**Step 28.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and see Table is deleted.

# End of Lab



# 3.crud-asg-alb-lab
**Step 1.Goto Ec2>Auto Scaling groups>test-asg>Edit**
- Edit test-asg - 
- Desired - 3
- Minimum - 3
- Maximum - 3

- Health checks
  - Select ELB
  - Health check grace period - 60 seconds

Click on Update

**Step 2.Goto Ec2>Target Groups>Create Target Group**
In Basic configuration:-
- Choose Target - Select Instances
- Target group name - "tg-crud"
- Protcol,Port - HTTP,3000
- Everything else is same

Click on Next

**Step 3.Register targets**
- Select all 3 instances created by "test-asg"
- Click on Include as pending below

Click on Create target group

**Step 4.Goto AWS Console>All services>EC2>Load Balancing>load balancers>create load balancer**
- Select Application Load Balancer
- In Configure load balancer
  - Name - alb-crud
  - Scheme-internet facing
  - IP address type-ipv4
  - Listeners
    - Protcol - HTTP
	- Port    - 80
- Availability zones 
  - select vpc as default 
  - select all 3 AZs

Click on Configure security settings

**Step 5.Click on Configure security groups**
- Select security group

Click on Next:Configure routing

**Step 6.In Configure routing**
- Target group - Existing target group
- Name - tg-crud
- Everything as same

Click Next:Register targets

**Step 7.Click on Next:review**

**Step 8.Click on Create**

**Step 9.Click on Close**

**Step 10.Goto Ec2Dashboard>Load balancers>alb-crud**
- It is in provisioning state
**Step 8.Goto Ec2Dashboard>Auto Scaling>Auto scaling groups>test-asg**
- Load balancing>load balancers
  - Select Application Load Balancer
    - also select target group as tg-crud |HTTP
  
Click on Update

**Step 11.Goto Ec2>Target groups>tg-crud**
- See that instances are Healthy now

**Step 12.Goto Ec2Dashboard>Load balancers>alb-crud**
- It is in active state

**Step 13.Copy DNS name of alb-crud**
- Paste it in the Browser and see that Application is Running
- ALB is passing traffic equally to all instances

**Step 14.Open Postman Tool**
- Click on Top Right corner Manage Environments
- In Add Environment - Name as "ALB"
  - Variable - url
  - Initial Value - DNS name of ALB e.g-http://alb-crud-xxxxx.ap.south-1.elb.amazonaws.com
  - Click on ADD and close this window

- Now change the Environment to ALB 

**Step 15.Now select {{url}}/create table and Click on Send**

**Step 16.Check the Table created in DynamoDB**
- Goto AWS Console>DynamoDB>Tables
  - Table is created

**Step 17.Goto Postman Tool and put the following value**
- http://{{url}}/readData

**Step 18.Goto Postman Tool and put the following value**
- http://{{url}}/insertData

**Step 19.Now Goto AWS Console>DynamoDB>Tables>Items>info**
- New Item added successfully

**Step 20.Goto Postman Tool and put the following value**
- http://{{url}}/updateData

**Step 21.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data updated

**Step 22.Goto Postman Tool and put the following value**
- http://{{url}}/deleteData

**Step 23.Now Goto AWS Console>DynamoDB>Tables>Items>info>actors**
- Check for the data deleted

**Step 24.Goto Postman Tool and put the following value**
- http://{{url}}/deleteTable

**Step 25.Now Goto AWS Console>DynamoDB>Tables**
- Refresh and see Table is deleted.

**Step 26.Ec2 Dashboard>Security Groups>sg-xxxx>Edit Inbound rules**
- In Custom TCP | 3000 - Select Source as "Security group of ALB"
- Now Traffic will be served only to ALB's DNS only and not for individual instance

# End of Lab



