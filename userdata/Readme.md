# 1. Install AWS CLI
sudo snap install aws-cli --classic

# 2. Copy WAR file from S3 to /tmp
aws s3 cp s3://vprofile-sre/vprofile-v2.war /tmp/

# 3. Stop Tomcat
sudo systemctl stop tomcat10

# 4. Clean existing deployment
sudo rm -rf /var/lib/tomcat10/webapps/ROOT/

# 5. Deploy new WAR
sudo cp /tmp/vprofile-v2.war /var/lib/tomcat10/webapps/ROOT.war

# 6. Start Tomcat
sudo systemctl start tomcat10

# 7. Verify deployment
ls /var/lib/tomcat10/webapps/
