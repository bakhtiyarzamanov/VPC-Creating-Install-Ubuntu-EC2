Created VPC 
1.	Select IPv4 CIDR manual input
2.	Write IPv4 CIDR # 192.158.0.0/24
3.	No IPv6 CIDER block
Created 4 subnets
1.	Subnet public1
2.	Availability Zone 1a
3.	IPv4 CIDER – 192.158.0.0/26
4.	Subnet public2
5.	Availability Zone 1a
6.	IPv4 CIDER –192.158.0.64/26
7.	Subnet private1
8.	Availability Zone 1a
9.	IPv4 CIDER – 192.158.0.128/26
10.	Subnet private2
11.	Availability Zone 1a
12.	IPv4 CIDER – 192.158.0.192/26
Created and attached Internet GATEWAY TO VPC
Created the public and private Route Tables and attached it to VPC
1.	Added routes to them
2.	Go to subnet association select the top edit 
3.	In it I associated public 1 and 2 subnets with the Route tables
4.	And I associated private 1 and 2 subnets with the Route tables
Next in Subnet Public1 I created a NACL for Bastion Hosts is also called BOX or Jump Box or Jump Start (Bastion Hosts is basically an EC2. Bastion Host is used for private connectivity from public to private subnet) Elastic Cloud Compute
I explicitly defined inbound rules for NACL public1
1.	So, I gave a rule number 110
2.	Type should be Custom TCP
3.	Port range I gave 22 for SSH
4.	In Source section I used my local public IP (to get my local IP I went to www.whatos,myip.com or I can get it from my terminal. Just by typing ip config or you can get it from your vpc)
5.	So I put my IP address and at the end of it I put /32 which means only single IP
I explicitly defined outbound rules for NACL public1
1.	So, I gave a rule number 110
2.	Type should be Custom TCP
3.	Port range I gave 1024-65535

4.	In Source section I used my local public IP (to get my local IP I went to www.whatos,myip.com or I can get it from my terminal. Just by typing ip config or you can get it from your vpc)\
Then I tested  
5.	So I put my IP address and at the end of it I put /32 which means only single IP(NOTE: in the job environment I will be using the work IP)

Then I created a Security Group for bastion in public1 Subnet
1.	I named it and then selected my VPC
I explicitly defined inbound rules for Security Group public1
2.	Type I selected Custom TCP
3.	Port range typed 22 for SSH
4.	From Source dropdown menu selected My IP and my IP address pumped-up
I DIDN’T explicitly define outbound rules for Security Group because it is Stateful

Next I created an EC2 instance and name it BastionHost.
1.	I created an Amazon Machine Image
2.	Then Instant type
3.	I created a keypair, selected key pair type RSA and private keypair format .pem
4.	The key pair information downloads into your download folder
5.	Then I go to edit Network settings. Select my VPC and then select Subnet public1
6.	On Firewall I select existing Security Group 
7.	Enable Auto-assign public IP
8.	Common security groups section, from the dropdown menu I select bastion security group 
9.	And I launch my EC2
Next I created 1 NACL for my 2 private subnets
I explicitly created an inbound rules for NACL private1 and 2
1.	I give it a rule number 110
2.	Port range I give 22  (SSH) (So I will be able to connect Bastion in public Subnet 1 to private Subnets)
3.	110	SSH (22)	TCP (6)	22	192.158.0.0/24          Allow

4.	120	All ICMP - IPv4	ICMP (1)	All	0.0.0.0/0	 Allow
5.	130	Custom TCP	TCP (6)	1024 - 65535	0.0.0.0/0	 Allow
6.	*	All traffic	All	All	0.0.0.0/0

I explicitly created outbound for NACL private1 and 2
Rule number
Type 	Protocol	Port range	Destination	Allow/Deny

              110	HTTPS (443)	TCP (6)	443	0.0.0.0/0	 Allow
120	All ICMP - IPv4	ICMP (1)	All	0.0.0.0/0	 Allow
*	All traffic	All	All	0.0.0.0/0	 Deny


Next, I go to Subnet associations and associated private 1 and 2 subnets with Network ACL


Next, I created new security groups for EC2 instances in my private subnet 1 and 2
1.	So, I select a name for my SG
2.	Same name I Pass into Description section
3.	I select my own VPC

And I create an inbound rule for Security Groups for private1 and 2 EC2’s – 
1.	I select type 
2.	Custom TCP, 
3.	Port range 22, 
4.	CIDR blocks 192.158.0.0/24


And I create an outbound rule for Security Groups for private1 and 2 EC2’s – 
1.	I create 3 types – All traffic, HTTPS and All ICMP – Ipv4
2.	IN CIDR block I select 0.0.0.0/0
3.	Then save it
Then I created 2 EC2 instances for my 2 private Subnets
1.	I named my new EC2 instance 
2.	I selected Amazon Machine Image
3.	I selected Amazon Linux 2 Kernel 
4.	I selected Instance Type -t2.micro
5.	Key pair -bastion, which I have had created earlier. 
6.	I edited the Network settings 
7.	Network settings - added my VPC
8.	Network settings – selected private 1 subnet
9.	In Auto-assign public IP drop-down window is disabled  (because don’t need an internet connectivity in private environment)
10.	 Firewall (security groups) – selected existing security group
11.	Common security groups – selected pirvate_ec2 same as the name of the new EC2 instance
12.	Click save
Next I created the 3rd NACL for public2 subnet. 
I gave it an inbound and outbound rules
1.	So, rule numbe 110
2.	Type HTTPS
3.	Port range 433
4.	Source 192.158.0.0/24
5.	Allow
Then I give it one more rule 
1.	Rule number 120
2.	Port range 1024-65535
3.	Source 0.0.0.0/0
4.	Allow
Then I give it another one rule
1.	Rule number 130
2.	Type All ICMP-IPv4
3.	Port range All
4.	Source 0.0.0.0/0
5.	Allow
Then I configured the outbound rules too
1.	Rule number 110
2.	Type HTTPS 
3.	Port range 443
4.	Source 0.0.0.0/0
5.	Allow
Then I give it one more rule 
5.	Rule number 120
6.	Port range 1024-65535
7.	Source 192.158.0.0/24
8.	
9.	Allow
Then I give it another one rule
6.	Rule number 130
7.	Type All ICMP-IPv4
8.	Port range All
9.	Source 0.0.0.0/0
10.	Allow
11.	Save changes
Then I go to NAT gateways and create a NAT gateway in public2 subnet.
Remember NAT Gateway still does use internet. It still traverse through internet
Then I go to Subnet Association and edit it to associate NACL with public2 subnet
Next, I go to edit private rout table to 
1.	So, I go to Rout table
2.	Select private from rout table
3.	Edit routs
4.	Add route and I select 0.0.0.0/0 as a destination
5.	And I add Target – Nat Gateway 
6.	and nat-02d990c73aeb94250 pops-up in the target window
Next you go to Visual Studio Code and test connectivity to the internet
1.	type ping google.com  in VSC
2.	the result should show 0% packet loss – meaning you have the connectivity to the internet
Second Homework
I created an s3 bucket for saving the recorded data from VPC Flow Logs
Then I created a flow log
1.	Then I copied ARN from s3 bucket’s properties section. 
2.	And paste it to S3 bucket ARN window in flow log.
3.	Log record format I selected AWS default format
4.	In Log file format I selected Text(default)
5.	In Hive-compatible S3 prefix – I did not Enable 
6.	Partition logs by time I selected every 1 hour for demo purpose
REMEMBER: Whenever you configure your flow log, it automatically attaches a bucket policy in your s3 bucket. 
Next, I configured the CloudWatch. And connected VPC Flow logs to CloudWatch
1.	Under CloudWatch, in logs section I created a log groups
2.	I select never expire for demo purpose
Then I went to my VPC and in it I created another flowlog. This time not to s3 but to CloudWatch
1.	And in it I select the exact same things that I had previously selected in flowlog for s3
2.	I have to go back and create an IAM role 
3.	And then come back to Create a flow log section and select the IAM that I just created
Next, I created a CloudWatch Alarm in CloudWatch. And then I add SNS to my CloudWatch Alarm
1.	To do that I will go into my CloudWatch
2.	From there I go into my Log groups
3.	In it I should see the log streams
4.	Then I will enter into Metric filter
5.	So, I go to CloudWatch-Log groups
6.	In it I go to Metric filters
7.	There I select a Metric filter 
8.	Next I create an Alarm -In CloudWatch-Log groups
9.	Select Metric
10.	In it I go to Browse and select vpc flowlogs
11.	And then In Alarm I selected Period 1 minute for simplicity.  
12.	In Condition in Alarm I selected Static
13.	Greater/Equal
14.	And defined the threshold value by 1
15.	And click next
16.	Then in Create a new topic window I typed – Whenever-reject-traffic-happens 
17.	Email endpoints that will receive the notification – type – info@orchsky.com
18.	Then I click on Create topic
19.	In or der it to work, I have to go to my email and confirm it. 
20.	Then in CloudWatch – Alarms – Create alarm section – I give a name to Alarm. 
21.	I name Alarm SSH rejects and click Next
22.	And create an Alarm 









 










 















