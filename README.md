# VPC-Peering ⛓️
![image](https://github.com/user-attachments/assets/895ee794-101d-4f1b-8bc9-adc66f54ad79)


Hello Connections!

🚀Today, I'm thrilled to share my recent project on implementing AWS VPC Peering to enhance cross-VPC
communication! This setup enables secure, private, and low-latency communication between VPCs in
the AWS cloud – a powerful solution for multi-account or multi-environment architectures.

👨💻In this project, we established a VPC Peering connection, configured routing, and optimized network
communication for a seamless data flow between VPCs. This solution is perfect for applications
requiring robust, scalable, and cost-effective connectivity between cloud environments.

✨Ready to dive into AWS networking and take your cloud skills to the next level? This project is an
excellent learning opportunity!

Prerequisites

Before getting started, ensure you have:
- An AWS account
- Basic knowledge of AWS networking concepts (VPCs, subnets, route tables, NACL)
- Access to AWS CLI or AWS Management Console

Project Steps

Step 1: Set Up AWS Environment
Create VPC 1

Under Name tag auto-generation, enter NexatWork-1 / The VPC’s IPv4 CIDR block is already pre-filled
to (10.0.0.0/16) change that to (10.1.0.0/16) For IPv6 CIDR block, we’ll leave in the default option of No
IPv6 CIDR block. / For Tenancy, we’ll keep the selection of Default. / For Number of Availability Zones
(AZs), we’ll use just 1 Availability Zone. / Make sure the Number of public subnets chosen is 1. /
For Number of private subnets, we’ll keep thing simple today and go with 0 private subnets./ but we don’t need defined subnets for this one.
Let’s skip the Customize subnets CIDR for this project. / Next, for the NAT gateways ($) option, make
sure you’ve selected None. As the dollar sign suggests, NAT gateways cost money! / Next, for the VPC
endpoints option, select None. / You can leave the DNS options checked / Select Create VPC.

Create VPC 2

I am going to set up another VPC with slightly different settings / Select Create VPC. / Select VPC and
more. / Under Name tag auto-generation, enterNexaWork-2 / The VPC’s IPv4 CIDR block should be
unique! Make sure the CIDR block is NOT (10.1.0.0/16)- it should be (10.2.0.0/16) / For IPv6 CIDR block,
we’ll leave in the default option of No IPv6 CIDR block. / For Tenancy, we’ll keep the selection of Default.
/ For Number of Availability Zones (AZs), we’ll use just 1 Availability Zone. / Make sure the Number of
public subnets chosen is 1./ For Number of private subnets, we’ll go with 0 for today’s project. Let’s keep
it simple with just a single subnet! / For the NAT gateways ($) option, select none. / For the VPC
endpoints option, select None. / You can leave the DNS options checked. / Select Create VPC.

![VPC](https://github.com/user-attachments/assets/ed8235ee-0bdb-4591-a912-2ef4bb431693)
                          [Fig 1: Successfully created of VPC]

Step 2: Set Up VPC Peering Connection
Now that we have two VPCs ready to go, let’s bridge them together with a peering connection. / In the
VPC console, click on Peering connections on the left hand navigation panel. / Name your Peering
connection name as (VPC 1 <> VPC 2) / Select NexatWork-1-VPC for your VPC ID (Requester). /
Under Select another VPC to peer with, make sure My Account is selected. / For Region, select This
Region. / For VPC ID (Accepter), select NexaWork-2-VPC / Click on Create peering connection. / Your
newly created peering connection isn’t finished yet! The green success bar says the peering
connection has been requested. / On the next screen, select Actions and then select Accept request…/
Click on Accept request again on the pop up panel. / Click on Modify my route tables now on the top
right corner.

Step 3: Update Route Tables
In this step, we’re going to:
Set up a way for traffic coming from VPC 1 to get to VPC 2.
Set up a way for traffic coming from VPC 2 to get to VPC 1.
Update VPC 1’s route table
Select the checkbox next to VPC 1’s route table i.e. called NexaWork-1-rtb-public.
Scroll down and click on the Routes tab. / Click Edit routes. / Let’s add a new route! / Add a new route
to VPC 1 by entering the CIDR block (10.2.0.0/16) as our Destination. / Under Target, select Peering
Connection. / Select VPC 1 <> VPC 2. / Click Save changes again.
Update VPC 2’s route table
We are going to repeat the steps as we did for VPC 1 Route table but this time we are setting
the destination to “10.1.0.0/16” and for our Target, select “ Peering Connection VPC 1<>VPC2” and save
changes.

![routetable](https://github.com/user-attachments/assets/e87957e2-ecd9-4578-882a-7cfb45ab1ec3)
![routetable2](https://github.com/user-attachments/assets/dc2158d6-513d-4c34-8a15-19c2b0aaf859)
                          [Fig 2: Successfully created of Routetable]

Step 4 : Launching EC2 Instances
In this step, we are going to launch an EC2 instance in each VPC, so we can use them to test our VPC
peering connection later. Launch an instance in VPC 1 / Head to the EC2 console — search forEC2in the
search bar at the top of screen. / Select Instances at the left hand navigation bar. / Select Launch
instances. / Since our first EC2 instance will be launched in our first VPC, let’s name it Instance-NexaWork
VPC 1 / For the Amazon Machine Image, select Amazon Linux 2023 AMI. / For the Instance
type, select t2.micro. / For the Key pair (login) panel, select Proceed without a key pair (not
recommended). / At the Network settings panel, select Edit at the right hand corner. / Under VPC,
select NextWork-vpc-1. / Under Subnet, select your VPC’s public subnet. / Keep the Auto-assign public
IP setting to Enable for Both EC2 Instances. / For the Firewall (security groups) setting, Amazon VPC
already created a security group for your VPC — let’s use that! / Choose Select existing security group. /
Select the default security group for your VPC. / Select Launch instance.

![nexaworkinstance](https://github.com/user-attachments/assets/b7df1d48-9a18-4b24-9ab4-9870c662f742)
                              [Fig 3: Successfully created of Instances]

Connect to Instance 1
To test our VPC peering connection, we’ll need to get one of our EC2 instances to try talk to the other. In
this step, you’re going to: Use EC2 Instance Connect to connect to your first EC2 instance. Select
connect...Oh no! We’ve failed to connect to our instance / Head back to your VPC console. /
Select Subnets from the left hand navigation panel. / Select the checkbox next
to NextWork-1-subnet-public1 / let’s take a look! Investigate the Route table and Network ACL tabs. /
Everything looks good! Our Network ACL allows all traffic in and out, and our route table is correctly
setting up a route to the Internet Gateway. / That leaves one more thing to investigate / Copy the VPC
ID of NextWork-1-vpc. / Head into the Security groups page from the left hand navigation panel. / Whoa!
it’s a bunch of nameless security groups! / To find the VPC 1’s default security group, let’s use the handy
search bar. / Click into the search bar, and paste the ID you copied into the search bar. Make sure there
aren’t any empty spaces before your text. / Select the filter! / Nice, that narrowed it nicely for us. Select
the checkbox next to VPC 1’s default security group. / Select the Inbound rules tab./ Aha! Mystery
solved.

The answer lies in the Source of our security group’s inbound rules. / We’re trying to access Instance —
NextWork VPC 1 using SSH through EC2 Instance Connect, which is trying to connect to your instance
over the internet. / Our default security group only allows inbound traffic from within the VPC, so traffic
from the internet is being cut off! / That’s a key learning to take away: The default security group for a
new VPC does not allow incoming traffic from outside of the VPC. / You have to allow inbound SSH traffic
on port 22 yourself for both VPC one by one . / In the Inbound rules tab, select Edit inbound rules. /
Select Add rule. / For your new rule, configure the Type as SSH. / Then, under Source type,
select Anywhere-IPv4. / Select Save rules. / With that modified, refresh your EC2
console’s Instances page. / Select your Instance-NexaWork VPC 1 and select Connect again. /
Select Connect in the EC2 Instance Connect setup page.

Step 5: Testing VPC Peering
Let’s see if we can connect with VPC 2’s instance from here.
In this step, we are going to / Get Instance 1 to send test messages to Instance 2. / Leave open the EC2
Instance Connect tab, but head back to your EC2 console in a new tab. / Select Instance —
NexaWork VPC 2. / Copy Instance — NexaWork VPC 2’s Private IPv4 address. / Switch
back to the EC2 Instance Connect tab. / Run Ping [the Private IPv4 address you just copied]in the
terminal. / You should see a response similar to to the ping you just entered / you’ve set up a
peering architecture that connects VPC 1 to VPC 2 AND validated it with ping / do same for VPC2 to VPC1

To test VPC peering, I ran the command `ping <destination Private IP>` from an instance in one VPC to an
instance in the peered VPC. This ensures connectivity across the peering connection. Make sure the
correct security group rules and route table entries are in place.
A successful ping test would validate my VPC peering connection because it demonstrates that instances
in the peered VPCs can communicate over the private IP addresses. It confirms that the route tables,
security groups, and network ACLs are correctly configured for connectivity.

![VPC1 Ping to 2VPC](https://github.com/user-attachments/assets/5e565fbd-669b-4bf7-a864-38d4523a8268)
         [Fig 4: Successfully created VPC Peering connection between `VPC-1` and `VPC-2`]

![VPC2 Ping to 1VPC](https://github.com/user-attachments/assets/21f8d635-165a-4876-bd1a-cd9d9eca6631)
[Fig 5: Successfully created VPC Peering connection between `VPC-2` and `VPC-1`]

NOTE: DELETE ALL YOUR RESOURCES!!!
