# AWS-Project
Server Based Architecture 

NOTE: After the Creation of the Website I terminated all the AWS Services I used because else I would ask to pay the charge for these services[ Pay-as-you-go pricing ]

A Server Based Architecture is a technique to deploy any application/ website into the public using manual configured servers. This involves manually provisioning of Servers in the cloud which runs the application/ website through the AWS Cloud. 
Amazon Web Services (AWS) offers a comprehensive set of cloud computing services, including infrastructure as a service (IaaS), platform as a service (PaaS), and software as a service (SaaS). One of the fundamental deployment models on AWS is the server-based architecture, where applications are deployed and managed on virtual servers in the cloud.



### Services Used :
 
- VPC: Virtual Private Cloud - A logically isolated section of the AWS Cloud where users can launch AWS resources in a virtual network.

- Subnets: Segments of a VPC's IP address range that are used to organize and manage network traffic.

- Internet Gateway: A component that allows communication between instances in a VPC and the internet.

- Route Table: A set of rules that determines where network traffic is directed within a VPC.

- Security Groups: Virtual firewalls that control inbound and outbound traffic for EC2 instances.

- EC2: Elastic Compute Cloud - Virtual servers that users can launch and manage within the AWS Cloud.
- S3: Simple Storage Service - A scalable object storage service designed to store and retrieve any amount of data from anywhere on the web.

- RDS: Relational Database Service - A managed database service that simplifies the setup, operation, and scaling of relational databases.

- DynamoDB: A fully managed No-SQL database service that provides fast and predictable performance with seamless scalability.

- Load Balancer: Distributes incoming application traffic across multiple EC2 instances to ensure fault tolerance and high availability.

- NAT Gateway: A managed network address translation service that allows instances in a private subnet to connect to the internet or other AWS services.

- Elastic IP: A static IPv4 address designed for dynamic cloud computing, allowing users to mask the failure of an instance or software by rapidly remapping the address to another instance.

- IAM: Identity and Access Management - A service that enables users to securely control access to AWS resources by creating and managing users, groups, and permissions.

- Target Group: A logical grouping of targets (such as EC2 instances) for routing traffic to as part of a load balancer's configuration.




### Steps: 
1.Create a Custom VPC [Virtual Private Cloud] to Internally distribute the working
[ VPC< Create VPC, Config the Information< Next ]

2.Create 3 subnets [2 public, 1 private ], subnets are particular servers which are now assigned to one particular VPC
[ subnet< Create Subnet< provide Availability Zone< subnet ip 10.0.1.0/24, Add new Subnet and same for all three< create]

3.These 3 Subnets are specifically created in different Availability Zones, Single Region 

[Why public, private subnets?]
Because 2 public subnets could work on the FrontEnd of the website which includes the Authentication, Authorization, API’s and Resources are publically available through internet gateway.
A private subnet can be used to Store the Backend of the Website where Data is not available directly through the Internet, data is authorized only to the authorized users. 

4.Create 2 Route Table [1 for Public subnet connected together, 1 for private subnet ] to route the authorized, incoming traffic based on their Destination.
[ Route Table< public< Create, same for private ]
 
5.Create a Internet Gateway to Route the Public subnet resources directly through the internet.
[ Internet Gateway< Name ]
[Attach this Internet Gateway to our Custom VPC ]

6.Connect the Route Tables according to the Diagram.1
[ Select Public route table< Subnet association< edit subnet association< select public subnet ] same for private subnet.
7.Create a Security Group

[ A security group is like a firewall which allows the incoming, outgoing traffic from that particular servers]
So, Create 1 security Group with [ Inbound rule: All Traffic, And a SSH client to connect out Ubuntu Server Directly ]
And Assign this security group rule, file created to every particular server 

[ Security group< name< Select our Custom VPC< Inbound rule< add rule< All Traffic< target: Anywhere< Create for a SSH Client< Create ]

8.Now, Create a Server in each Subnet [ Ec2 ]
We have Chosen Ubuntu Server with different Availability Zones to every particular Server.
[ Create a Key Pair and save the Password in the Local System ]
[ Our Servers: vm1, vm2, vm3(private) ]

[ EC2< Launch Instance< name< .ppk< Create< Network Setting<Custom VPC< subnet 1 < enable< Create security Group(Select existing one)< Launch instance ] Same created  2 more servers (vm1: public Server 1) (vm2: public Server 2)(vm3: private Server 3)

9.Create a Storage Type 
[ We created an Object Storage AWS S3 ]
[ S3< Buckets< Create Bucket< name< ACL enable< Unblock< versioning enable< create ]

10.Create a Database- to store Employee input Data
[ Relational Database, AWS RDS] and assigned it to our Custom VPC
[ VPC < Actions < Edit VPC Settings < Enable DNS Hostnames < Save ]

11.Create a Non Relational Database
[ AWS DynamoDB ] and assign it to our Custom VPC

12.Create a Load Balancer- 
[ AWS ELB- Elastic Load Balancer, assign it to our 3 subnets to distribute the load ]

13.Create a Target Group in Load Balancer to Target 1 particular VPC and assign it to our ( custom VPC )

14.Create a NAT Gateway( To Route private Subnet’s resources like Database information to the Internet Gateway)
[NAT(Network Address Translation) is used to hide the original IP and provide a fake ip to communicate with the internet gateway ]- Create a NAT Gateway with an Elastic IP
[ Mention this NAT gateway to our private subnet ]

15.Elastic ip
[ A Static IPv4 IP which is used to remap to different instances as needed, example: lets assume if any one server has ip as 312 and it gets crash then we could assign that 312 ip to another server so that the work should not stop or data should not loose ]

NOTE: You created a private subnet< NAT Gateway< Connected to it.
But did not permit to transmit the data through it 

16.IAM
[ Identity Access Management- is used to control and manage the user authentications, roles ]

IAM< Roles< CreateRole< AWS< AWS Service Role< Select EC2 Server< S3.FullAcess, RDS.FullAccess, DynamoDB.FullAccess< Provide a Role Name< Create

17.Assign this IAM Role to our Private Subnet
Private subnet< Actions< Security< Modify IAM Role< Select our Role< Update

18.Now Everything is coupled and distributed within a single VPC now its time to connect any one Server
[Why any one, because of Load Balancer everything could be automatically distributed ]





Connection :
1.Connect to any one server, copy the public IP of that server from instance
2.Download Putty Software, provide the IP and [ SSH< Authentication< Credentials< Browse ]and provide the key pair we saved in out local system 
3.Login as Ubuntu 

4.Install MySql to check is our Database Working 
[ apt install mysql -client -y]
[ mysql -h (EndPoint, from RDS) -u (username) -p] and provide the Master Password , while configuring our Database.

As Database is Connected,
-show Databases;
-use [ Database Name ]- RDS we Created
-show tables;
CREATE TABLE <TableName>(emp_id VARCHAR(20), first_name VARCHAR(20), last_name VARCHAR(20), primary_skills VARCHAR(20), location VARCHAR(20));

- Select*From<TableName>;
-Exit







Now as of my Project: 
I was Used Python language to get my interface, Python Frameworks
- sudo apt-get install python3

Create a new directory for your project and navigate into it:

- mkdir my_website
- cd my_website

Inside the directory, create a Python file (e.g., app.py) to define your Flask application:



from flask import Flask, render_template, request

app = Flask(__name__)

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/submit', methods=['POST'])
def submit():
    first_name = request.form['first_name']
    last_name = request.form['last_name']
    primary_skills = request.form['primary_skills']
    location = request.form['location']
    # Here you can process the received data (e.g., store it in a database)
    return 'Data received successfully!'

if __name__ == '__main__':
app.run(debug=True)






After this Save and Exit the File

- sudo apt-get install python3-flask -y
- sudo apt-get install python3-pymysql
- sudo apt-get install python3-boto3 -y

- sudo python3 <WebsiteName>.py

Inside the my_website directory, create a templates directory:

- mkdir templates

Inside the templates directory, create an HTML file (e.g., index.html) to define the layout of your webpage:

html
Copy code
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Employee Form</title>
</head>
<body>
    <h1>Employee Information Form</h1>
    <form action="/submit" method="post">
        <label for="first_name">First Name:</label>
        <input type="text" id="first_name" name="first_name"><br><br>
        <label for="last_name">Last Name:</label>
        <input type="text" id="last_name" name="last_name"><br><br>
        <label for="primary_skills">Primary Skills:</label>
        <input type="text" id="primary_skills" name="primary_skills"><br><br>
        <label for="location">Location:</label>
        <input type="text" id="location" name="location"><br><br>
        <input type="submit" value="Submit">
    </form>
</body>
</html>

Run Your Flask Application:

In the my_website directory, run your Flask application by executing the following command:

python3 app.py 

Go to Load Balancer< Copy DNS Name and Paste it on any browser< Website Running. 






Conclusion : 


We Created a Website Using AWS Services which contain 3 servers Tightly Coupled with each other with a Load Balancer. And With Data Abstraction we secured Database data in private subnet. With created a Virtual Private Network, a secured and a private network for our backend of the website with IAM providing roles to the authorized to access the private subnet. 
The Resources are Directly accessed with the Internet through Internet Gateway and Data like Database and private data is been hidden. 

While server-based architectures offer flexibility and control, they also require manual management of infrastructure components, including provisioning, scaling, and maintenance. This approach may be suitable for applications with specific infrastructure requirements or for organizations that prefer to have full control over their server environment. However, it may also involve higher operational overhead compared to serverless or managed services architectures.

The Manual Configuration of various AWS Services like: 
- Elastic Load Balancer 
- Non Relational Database
- Relation Database
- AWS S3 storage( Object Storage )
- AWS Virtual Private Cloud 
- Elastic IP
- AWS NAT ( Network Address Translation )
- AWS IAM ( Identity Access Management )
- AWS EC2 ( Virtual Servers )
