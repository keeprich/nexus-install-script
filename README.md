To install Nexus, we will connect via ssh to the nexus server

SSH into your server.


 update the yum packages. Also install required utilities.

```
yum update -y
sudo yum install wget -y
java -version
sudo yum install java-1.8.0-openjdk.x86_64 -y
```
 Download the latest nexus

```
cd /opt
sudo wget -O latest-unix.tar.gz https://download.sonatype.com/nexus/3/latest-unix.tar.gz
tar -xvzf latest-unix.tar.gz
sudo mv nexus-3* nexus
mv sonatype-work nexusdata
ls -lh
```
 Set User/Permissions and Configurations

```
useradd --system --no-create-home nexus
chown -R nexus:nexus /opt/nexus
chown -R nexus:nexus /opt/nexusdata
```
Edit /opt/nexus/bin/nexus.vmoptions file by changing sonatype-work to nexusdata as shown on the image below

```
vi /opt/nexus/bin/nexus.vmoptions
```
 

Edit nexus.rc file.

```
vi /opt/nexus/bin/nexus.rc
```
Uncomment run_as_user parameter and add new value.

```
run_as_user="nexus"
```
Configure the open file limit of the nexus user.

```
vi /etc/security/limits.conf
```
Add the below values to the file at the bottom.


nexus - nofile 65536

Set Nexus as a System Service

Create the Systemd service file in /etc/systemd/system/.

```
sudo vi /etc/systemd/system/nexus.service
```
Add the following contents to the unit file.

```
[Unit]
Description=Nexus Service
After=syslog.target network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Group=nexus
Restart=on-failure

[Install]
WantedBy=multi-user.target
```
Manage Nexus Service, Execute the following command to add nexus service to boot.

```
systemctl daemon-reload
systemctl enable nexus.service
systemctl start nexus.service
```
now lets open the nexus port (8081)

```
firewall-cmd --zone=public --add-port=8081/tcp --permanent
firewall-cmd --reload
```
restart the nexus service
```
systemctl restart nexus.service
```
verify the status

```
systemctl status nexus.service
```
Check the running service port.

```
netstat -tunlp | grep 9081
 ```

 Open Nexus in the browser

 At this level, we have installed nexus and it is running.

By default, nexus runs on port 8081.

Let's open the IP address of our nexus server followed by this port in a browser :

default user is admin and default password is in the /root/nexusdata/nexus3/admin.password file


 

to get the default password, use this command in the terminal of the nexus server


cat /root/nexusdata/nexus3/admin.password
enter the user name : admin, then the password


 

Set up the new password for the admin user


check enable anonymous access in the next windows then click to the next


Click on Next then Finish

Nexus is up and running


