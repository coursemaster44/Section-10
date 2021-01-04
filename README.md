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


