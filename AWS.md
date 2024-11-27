# AWS

Amazon Web Service stuff

## Create AWS Free Tier Account

- Google `aws free tier` and create a free account
- Go to `aws.amazon.com` and _Sign in to the Console_ -> Root user and log in
- Choose `N.Virginia` from the top right corner next to your username
- Clone this repository for resources `https://github.com/nealdct/aws-clf-code`

#### Configure Account and Create a Budget and Alarm

Configure Account

- Go to `IAM Dashboard` (choose _IAM_ from services)
- Create Alias from the `AWS Account` section on the right
- Go to your account (Click your name on the top right -> Account)
- Active `IAM user role access to Billing information`
- Choose _Billing Preferences_ on the left navbar under the `Preferences and Settings`
- Edit _Alert preferences_ -> put ticks on both boxes
- Activate `Invoice delivery preferences`

Budget

- Click _Budget_ on the left under the `Budgets and Planning`
- `Create Budget` -> Use a template -> Monthly cost budget -> Give a name and your budget (2e)
- _Cost Explorer_ under the `Cost Analysis` shows where you are spending your money

#### Creating IAM Users and Groups

Group

- Go `IAM` -> Create User groups -> Create group
- Group name `Admins`
- `Attach permission policies` -> _AdministorAccess_ -> Create group

Users

- Click _Users_ under the `Access managment` -> Create user
- Give User name -> Provide user access to the AWS Managment Console -> I want to create an IAM user
- Set Custom password -> Remove the tick from 'Users must create a new password at next sign-in' -> Next
- Add user to group -> Admins -> Next -> Create user -> Return to users list
- Try your new user -> Go to aws signin page -> Choose IAM user -> `Account ID` is the Account name of your root user -> Give the user name and password that you just created and it works
- It's better to log in with the `IAM user` account than the root account

#### Install Tools and Configure AWS Cli

- Install VS Code & AWS CLI Command Line Interface

Cloudshell

- Search `Cloudshell` from AWS Services
- Test it with command `aws help` (q) to quit

## AWS Authentication and Access Control

#### Setup Multi-Factor Authentication (MFA)

- Go `IAM Dashboard` -> `Users` from the left navbar -> Click your name -> _Security credentials_ -> Assign MFA device
- Give Device name -> Authenticator App -> Read the QR-code etc

#### Switching IAM Roles

- `IAM Dashboard` -> Users -> Create user -> Give User name and Provide user access to the AWS Managment Console -> Create IAM user -> Custom Password -> Remove tick from _Users must create a new password..._ -> Next -> Create user -> Return to users list
- Sign in with that user -> A lot of _Access denied_ everywhere
- Go `EC2` Service -> Lot of red -> Log out
- Login with that `IAM user` with Admin access -> `IAM` -> Roles -> Create role -> AWS account -> Next -> Search EC2 -> AmazonEC2FullAccess -> Next
- Role name: _ec2-role_ -> Create role -> Copy the 'Link to switch roles in console'
- Log in with the IAM user with no permissions in a incognito tab -> Click username in the top right navbar -> Switch role -> Insert fields manually OR open a new tab and paste the copied link
- Add IAM role name to Display name also -> Switch role -> ?? Nothing happens because user doesn't have any permissions
- Copy the ARN from the ec2-role summary with the IAM user with Admin permissions
- Users -> click the username with no permissions -> Add permissions -> Create inline policy -> JSON from the top -> Add this (paste the ARN from role summary to "Resource")

```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "sts:AssumeRole",
    "Resource": "arn:aws:iam::279455171996:role/ec2-role"
  }
}
```

-> Next -> Policy name _stsAssumeRole_ -> Create policy

- Go back to that incognite window and try to Switch role -> now you see your role on the top right of the navbar
- Go to `EC2` and now most of the errors are gone -> Switch back to normal role

#### IAM Identity Center in Action

User with Admin permissions

- Go to `AWS Organizations` -> Create an organization
- Go to `IAM Identity Center` -> Enable
- _Manage permissions for multiple AWS accounts_ -> Click -> Create permission set -> Predefined permission set -> Next, Next, Create
- Create another permission set -> Predefined -> ViewOnlyAccess -> Next, Next, Create
- `IAM Identity Center` Dashboard -> Groups -> Create group -> Group name _Management_ and Create group
- `IAM Identity Center` Dashboard -> Users -> Add user -> Fill the fields -> Next -> Add user to the _Management_ group -> Add user
- `IAM Identity Center` Multi-account permissions -> AWS accounts -> Select the management account and Assign users or groups -> Management -> Next -> Select both _ViewOnlyAccess_ and _AdministratorAccess_ -> Next, Submit
- Now check your email and accept the Invitation -> Set your password and sign in -> set MFA
- You can see the AWS access portal URL in the `IAM Identity Center` Dashboard in Settings summary

## AWS Compute Services

#### Launching Amazon EC2 Instances

- `EC2` Service -> Launch instance

##### Linux instance

- Given instance name -> Quick Start _Amazon Linux_ -> Instance type _t2.micro_ -> Key pair `Create new key pair` -> Give key pair name, RSA, .pem, _Create key pair_ downloads the file on your computer

- Edit _Network settings_ -> Create security group, give Security group name _WebAccess_ and copy it also in the Description -> Launch instance
- Then _View all instances_ and it should be pending

##### Windows instance

- Give instance name -> Quick Start _Windows_ (Microsoft Windows Server 2022 Base) -> Instance type _t2.micro_ -> Choose the keypair that you created -> Network settings _Select existing security group_ and choose the _WebAccess_ what you just created -> Launch instance
- Choose _Windows-Server_ -> Security tab, click on Security groups (WebAccess) -> Edit inbound rules, Add rule -> Type: _RDP_, Source _Anywhere-IPv4_ -> Save rules

#### Connecting to Amazon EC2

##### Linux

- Choose the Linux-Server from the instances list and _Connect_ on the top ->
  _EC2 Instance Connect_ tab, _Connect using EC2 Instance Connect_ -> Connect
- Now it opens a terminal in a new window -> Test that everything is working, 'ping google.com' and 'Ctrl-C' to quit pinging
- REMEMBER TO STOP THE SERVER

##### Windows

- Download `Microsoft Remote Desktop` (google it or 'rdp for mac' and download some program)
- Choose the Windows-Server from the instances list and _Connect_ on the top -> _RDP client_ tab, Copy the _Public DNS_
- Open _Microsoft Remote Desktop_, add PC -> PC name: `Public DNS` that you just copied -> Add
- Get the password for the PC -> Go back to the _Connect to instance_ with the windows server and click on _Get password_ on the bottom of the page -> Upload the private key file what you have done earlier -> Decrypt password
- Copy the password -> Go back to _Microsoft Remote Desktop_, double click on the pc, Username: `Administrator` Password: `the copied password` -> Continue
- If it doesnt work, make sure that you have port 3389 open in the _Security rules_
- REMEMBER TO STOP THE SERVER

#### Create a Website with User Data

- `EC2` -> Instances, Launch instances -> Name: _WebServer_, _Amazon Linux_, _t2.micro_, no need for key pair -> _Network settings_ Select existing security group: _WebAccess_
- `Advanced details` at the bottom of the page and find _User data_ -> Choose file -> `(from the git repo that you cloned in the beginning)/amazon-ec2/user-data-web-server.sh` -> Launch instance
- _Instances_, Choose _WebServer_, _Security_ tab, click on the _WebAccess_ security group -> Edit inbound rules -> Add rule, Type: `HTTP` Source: `Anywhere-IPv4` -> Save rules
- _Details_ tab of your _WebServer_, copy _Public IPv4 address_ -> Open browser, paste the address -> Press Enter
- REMEMBER TO STOP THE SERVER

#### Practise with Access Keys and IAM Roles

- Connect to _Linux-Server_ on `EC2 instances` (Connect using EC2 Instance Connect) -> new you have the Amazon Linux 2023 terminal open
- `aws s3 ls` to terminal -> 'unable to locate credentials' -> Open `IAM` Service in a new tab -> Users, Choose user, Security credentials, Access keys, Create access key -> Command Line Interface (CLI), Next, No description needed, Create access key
- Copy the Access key -> Go back to the _Amazon Linux_ terminal -> `aws configure`, paste the access key -> Go back to the another tab and copy _Secret access key_, paste the secret access key -> Give the region name (us-east-1), Enter, Enter
- `aws s3 ls` again and the error is gone
- Make a `s3` bucket -> `aws s3 mb s3://mybucket-something2323` -> `aws s3 ls` now shows the bucket just created
- There is a security problem, you can see the credentials by just running `cat ~/.aws/credentials` command. So lets delete the the folders from _.aws_ folder -> `rm -rf ~/.aws/*`
- Go back to `IAM` Service and find the _Access keys_ -> Actions, Deactive -> Actions, Delete
- Choose _Roles_ on the navbar on the left -> Create role -> Trusted entity type: AWS service, Use case: EC2, Next -> Search s3, AmazonS3ReadOnlyAccess, Next -> Role name: S3ReadOnly, Create role
- `EC2 Service`, Instances -> Choose _Linux-Server_, Actions, Security, Modify IAM role -> S3ReadOnly, Update IAM role
- Back to the terminal -> `aws s3 ls` should show your bucket now
  -REMEMBER TO STOP THE SERVER

#### Launch Docker Containers on AWS Fargate

- Search for `Amazon Elastic Container Service`, Clusters, Create cluster
- Cluster name: _my-fargate-cluster_, Infrastucture: AWS Fargate, Create -> Check what happens in _View in CloudFormation_
- _Task definitios_ on the left navbar, Create new task definition -> Task definition family name: nginx-container, others on default -> _Container - 1_, Name: nginx-container, Image URI: nginx:latest -> Create
- Now back to _Clusters_ on the left navbar -> my-fargate-cluster -> Tasks tab -> Run new task
- On _Deployment configuration_ Family: nginx-container -> On _Networking_, Choose security group: WebAccess -> Create
- Click on the _Task_ id -> _Networking tab_ Copy Public IP -> Test it in the browser
- Back to _Clusters_, my-fargate-cluster, _Tasks_ tab, stop the running cluster
- _Services_ tab -> Create -> Family: nginx-container, Service name: my-service, Desired tasks: 2 -> On _Networking_ change to _WebAccess_ security group -> Create
- REMOVE TO STOP/DELETE THE CLUSTER

## AWS Storage Services
