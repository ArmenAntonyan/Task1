# INSTALLING ZABBIX SERVER AND ZABBIX AGENT

Step 1: Install 2 different Linux OS in VM (Ubuntu 22.04 for Zabbix and Ubuntu 20.04 for the agent).

Step 2: Start Ubuntu 22.04.

Step 3: In the browser, visit Zabbix.com and click on "Download."

Step 4: Choose Zabbix Packages.

Step 5: In the dropdown list, select Zabbix version - 6.4, OS Distribution - Ubuntu (arm64), OS version - 22.04.
Zabbix Component - SERVER FRONTEND AGENT, Database - MySQL, WEB Server - Nginx.

Step 6: Install Zabbix repository.

```sh
sudo wget https://repo.zabbix.com/zabbix/6.4/ubuntu-arm64/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.4-1+ubuntu22.04_all.deb
sudo apt update
```
Step 7: Install Zabbix server, frontend, and agent.
```
sudo apt install zabbix-server-mysql zabbix-frontend-php zabbix-nginx-conf zabbix-sql-scripts zabbix-agent
```
Step 8: Install MySQL server 
```
sudo apt install mysql-server
```

Step 9: Create the initial database (Make sure you have the database server up and running).
Run the following on the database host.
```
sudo mysql -uroot -p
```
Enter the password when prompted, then execute the following MySQL commands:
```
mysql> create database armen-zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by '       ';
mysql> grant all privileges on zabbix.* to armen-zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;
```

Step 10: On the Zabbix server host  prompted to enter the newly created password.

```
sudo mysql -uroot -p
password
mysql> set global log_bin_trust_function_creators = 0;
mysql> quit;
```
Step 11: Configure the database for Zabbix server.
Edit the file /etc/zabbix/zabbix_server.conf and set the password for DBPassword.
```
DBPassword=password
```

Step 12: Configure PHP for the Zabbix frontend.
Edit the file /etc/zabbix/nginx.conf, uncomment and set the 'listen' and 'server_name' directives.
```
# listen 8080;
# server_name example.com;
```
Step 13: Start Zabbix server and agent processes and enable them to start at system boot.
```
# sudo systemctl restart zabbix-server zabbix-agent nginx php8.1-fpm
# sudo systemctl enable zabbix-server zabbix-agent nginx php8.1-fpm
```
Step 14: Check the status of the services.
```
# sudo systemctl status zabbix-server zabbix-agent nginx php8.1-fpm
```
Ensure that all services are active.

# ZABBIX SERVER CONFIGURATION

Step 1: Configure /etc/zabbix/php-fpm.conf
Edit the file /etc/zabbix/php-fpm.conf and add the following line to allow connections from Zabbix agent server's IP address (assuming the agent server's IP is 192.168.74.9):
```
listen.allowed_clients = 127.0.0.1, 192.168.74.9
```
Step 2: Configure /etc/zabbix/zabbix_agentd.conf
Edit the file /etc/zabbix/zabbix_agentd.conf and set the following parameters:
```
Server=127.0.0.1,192.168.74.9
ServerActive=127.0.0.1,192.168.74.9
Hostname=HelloWorld
```
Replace HelloWorld with the desired hostname for the Zabbix agent.

Step 3: Configure /etc/zabbix/zabbix_server.conf
Edit the file /etc/zabbix/zabbix_server.conf and set the following database configuration parameters:
```
DBName=armen_zabbix
DBUser=armen_zabbix
DBPassword="my password"
```
Replace "my password" with the actual password for the Zabbix database user.

Step 4: Start Zabbix server and agent processes and enable them to start at system boot.
```
sudo systemctl restart zabbix-server zabbix-agent nginx php8.1-fpm
sudo systemctl enable zabbix-server zabbix-agent nginx php8.1-fpm
```
Step 5: Check the status of the services.
```
sudo systemctl status zabbix-server zabbix-agent nginx php8.1-fpm
```
Ensure that all services are active.

# INSTALL ZABBIX AGENT

Step 1: Start Linux Ubuntu 20.04.

Step 2: In the browser, visit Zabbix.com and click on "Download."

Step 3: Choose Zabbix Packages.

Step 4: In the dropdown list, select Zabbix version - 6.4, OS Distribution - Ubuntu (arm64), OS version - 20.04.
Zabbix Component - AGENT.

Step 5: Install Zabbix repository.
```
# sudo wget https://repo.zabbix.com/zabbix/6.4/ubuntu-arm64/pool/main/z/zabbix-release/zabbix-release_6.4-1+ubuntu20.04_all.deb
# sudo dpkg -i zabbix-release_6.4-1+ubuntu20.04_all.deb
# sudo apt update
```
Step 6: Install Zabbix agent.
```
# sudo apt install zabbix-agent
```
Step 7: Start Zabbix agent process and enable it to start at system boot.
```
# sudo systemctl restart zabbix-agent
# sudo systemctl enable zabbix-agent
```
ZABBIX AGENT CONFIGURATION
ep 1: Configure /etc/zabbix/zabbix_agentd.conf
Edit the file /etc/zabbix/zabbix_agentd.conf and set the following parameters:
```
Server=127.0.0.1,192.168.74.7
ServerActive=127.0.0.1,192.168.74.7
Hostname=Hello World
```
Replace HelloWorld with the desired hostname for the Zabbix agent.

Step 2: Restart Zabbix agent process and enable it to start at system boot.
```
# sudo systemctl restart zabbix-agent
```
Step 3: Check the status of the service.
```
# sudo systemctl status zabbix-agent
```
Ensure that the service is active.

# RUN ZABBIX SERVER GUI

Step 1: Check the IP address of the Zabbix server (e.g., my IP was ```192.168.74.7```).

Step 2: Open a browser and type in ``` 192.168.74.7:8080.``` (your Zabbix server ip and port)

Step 3: The Zabbix page will open. Choose the preferred language and press the "Next step" button.

Step 4: In the "Configure DB connection" step, choose DB name - ```"armen_zabbix"```,``` User - "armen_zabbix"```, and the password ```your password```. Then press the "Next" button.

Step 5: Set the server name to ```"armen_zabbix"``` and press the "Next step" button. Finally, press "Finish."

# ZABBIX SERVER GUI

Step 1: In the left list, choose ```"Monitoring."```

Step 2: In the dropdown list, choose ```"Hosts."```

Step 3: On the right side, choose ```"Create host."```

Step 4: In the Host section, write the Host name (it should be the same as Hostname= from /etc/zabbix/php-fpm.conf).

Step 5: In templates, choose ```"Linux by Zabbix agent."```

Step 6: In host groups, choose ```"Linux Server."```

Step 7: Click ```"Add"``` and choose ```"Agent."```

Step 8: In Agent IP address, write the IP of the Zabbix agent server ```(e.g., my IP was 192.168.94.9)```.

Step 9: Click the ```"Add"``` button.

Zabbix monitoring host added to the list.

Step 10: Click on the hostname ```(in this case, it was "HelloWorld")```.

Step 11: In the dropdown list, choose ```"Items."```

Step 12: The opened page should have the chosen ```(Hosts)``` hostname ```(in this case, it was "HelloWorld").```

Step 13: Choose ```"Monitoring"``` and click on ```"Hosts.```" After 30 seconds, the Availability will change color to green.
