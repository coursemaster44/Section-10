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


# 
