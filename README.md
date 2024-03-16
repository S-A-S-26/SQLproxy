# SQLproxy
Proxy data

```$ mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt 'ProxySQL Admin> '``` in this the port 6032 is for admin login and configuration.

#### Main things to configure for read write proxy mysql
1. mysql_servers - specify which servers will be used with their hostgroup_id,hostname,port ```INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (1,'10.0.0.1',3306);``` this is also known as adding backends.
2. mysql_users - application user's allowed to connect to database using the mysql proxy.
3. mysql_replication_hostgroups - is used to define the hostgroups like 1 belongs to readers group , 2 belongs to writer's group etc.
4. mysql_query_rules - is used to define which query should proxy to where like if it is ^select* statement then it should go to reader's group , if it is ^insert* statement's then it should go to writer's group. 
