App — Lift & Shift Architecture on AWS

This project demonstrates a full Lift and Shift deployment of the vProfile Java-based application on AWS infrastructure using EC2, Auto Scaling, Load Balancer, Route 53, and S3.

🏗️ Architecture Summary — vProfile Lift & Shift on AWS

🔐 Security Groups & Key Pairs

Created 3 Security Groups:

sg-1 for ALB – allows HTTP (80), HTTPS (443), SSH (22)

sg-2 for App Server – allows:

8080 from sg-1 (ALB)

SSH from your IP

sg-3 for Backend (DB, RabbitMQ, Memcached) – allows:

3306, 11211, 5672 from sg-2 (App Server)

All traffic from itself (for inter-backend comm)

SSH for manual access

Created EC2 Key Pair for secure SSH access.

📦 EC2 Instance Launch + Internal DNS

Launched 4 EC2 instances:

App Server

DB (MySQL)

Memcached

RabbitMQ

Route 53 Hosted Zone:

Created a private hosted zone.

Added records (name → IP) as per application.properties for internal name-based communication.

Example: db.vprofile.internal → <DB IP>

☁️ S3 + Artifact Packaging

Created S3 bucket (e.g., vprofile-sre)

On local machine:

Installed Maven, JDK, AWS CLI

Ran:

mvn install
aws s3 cp target/vprofile-v2.war s3://vprofile-sre/

IAM Setup:

Created IAM User with S3FullAccess

Configured access key in local machine using aws configure

📤 WAR Deployment on App Server

Install AWS CLI on app server:

sudo snap install aws-cli --classic

Fetch WAR from S3:

aws s3 cp s3://vprofile-sre/vprofile-v2.war /tmp/

Deploy to Tomcat:

sudo systemctl stop tomcat10
sudo rm -rf /var/lib/tomcat10/webapps/ROOT/
sudo cp /tmp/vprofile-v2.war /var/lib/tomcat10/webapps/ROOT.war
sudo systemctl start tomcat10

App now runs on http://<app-server>:8080

🌐 Load Balancer (ALB) + HTTPS

Created Target Group (port 8080)

Registered App Server in it

Created ALB with:

Listener on HTTP (80) → Forward to TG

Listener on HTTPS (443) using ACM Cert

ACM Validation done via:

DNS (Route 53) — Added CNAME record

📈 Auto Scaling Group (ASG)

Created AMI from App Server

Created Launch Template using the AMI

Created ASG with:

Launch template

ALB Target Group attached

Min/Max/Desired capacity setup

Scaling policies (optional)

✅ Result

Application now:

Runs securely behind ALB

Scales with ASG

Uses S3 for deployment

Has private DNS for internal communication