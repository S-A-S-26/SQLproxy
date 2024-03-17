# SQLproxy
Proxy data

```$ mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt 'ProxySQL Admin> '``` in this the port 6032 is for admin login and configuration.
6033 is for app connections.

#### Main things to configure for read write proxy mysql
1. __mysql_servers__ - specify which servers will be used with their hostgroup_id,hostname,port ```INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (1,'10.0.0.1',3306);``` this is also known as adding backends.
2. __mysql_users__ - application user's allowed to connect to database using the mysql proxy.
3. __mysql_replication_hostgroups__ - is used to define the hostgroups like 1 belongs to readers group , 2 belongs to writer's group etc.
4. __mysql_query_rules__ - is used to define which query should proxy to where like if it is ^select* statement then it should go to reader's group , if it is ^insert* statement's then it should go to writer's group. 

#### Users to create.
- __monitor user__- to monitor the stats from my sql **this is done in mysql login*
  - create a user in mysql first with '%' all host access ```create user proxy_mon@'%' identified by 'MySQL@321';``` in this the identified by denotes the password
  - give permissions only specific permissions are needed in this eg: all are given ```grant all on *.* to proxy_mon@'%';```
  - run ```flush privileges;``` to apply changes
- Define in it proxysql the newly created monitor user and its password to proxysql variables **this is done in proxysql login*
  -  ```set mysql-monitor_password='MySQL@321';```
  -  ```set mysql-monitor_username='proxy_mon';```
  -  ```load mysql variables to runtime;```
  -  ```save mysql variables to disk;```
- Create an application user who can access the database via proxy *this needs to be done in mysql login*
  - ```create user app@'%' identified by 'MySQL@321';``` 
  - ```grant all on *.* to app@'%';```
  - ```flush privileges;```
- Set this application user to the proxysql also known as whitelisting *this needs to be done in proxysql login*
  - ```insert into mysql_users (username,password,active,default_hostgroup,max_connections) values ('app','MySQL@321',1,10,100)``` hostgroup add from whatever host groups you have created mine was 10 as read hostgroup
  - to check use ```select * from mysql_users;```
  - repeat the runtime and save disk as always for proxysql
- To check ip and port in terminal enter  ```netstat -tulnp | grep -i proxysql```
- ```hostname -I``` display the IP addresses associated with the system's hostname.

- Finally enter into the mysql app connection port 6033 and test it.
  -```mysql -h'enter hostname here for proxysql' -P6033 -uapp -p'MySQL@321' -e"create database test"``` -u give the mysql_user that was created in previous step -p and also provide the password for the same.
