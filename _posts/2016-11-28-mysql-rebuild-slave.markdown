---
published: true
title: Mysql - rebuild slave from master
layout: post
---

Problem: Sometimes mysql master and slave go out of sync, this could happen due to various reasons like master stops writing to binary logs, duplicate entry exists in slave or some other insert/update constraint failure in slave (this could happen when slave database is not read only and some clients connects directly to slave). In such scenarios, the only way to bring back master and slave in sync is to rebuild slave from master. Below are tried and tested steps to rebuild slave from master where the master-slave setup already exists.

Consider this scenario with master and slave server as given below:

master/slave
exdap211/exdap212

__**Problem on slave**__

```bash
mysql> show slave status \G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: exdap211.xyz.in
                  Master_User: slave_sec
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000044
          Read_Master_Log_Pos: 552489846
               Relay_Log_File: mysqld-relay-bin.000041
                Relay_Log_Pos: 390393576
        Relay_Master_Log_File: mysql-bin.000044
             Slave_IO_Running: Yes
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 1452
                   Last_Error: Error 'Cannot add or update a child row: a foreign key constraint fails...'
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 390393413
              Relay_Log_Space: 552490230
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 1452
               Last_SQL_Error: Error 'Cannot add or update a child row: a foreign key constraint fails...'
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 100412
                  Master_UUID: f20637be-3530-11e5-a860-005056a05b60
             Master_Info_File: /var/db/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State:
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp: 160322 16:12:05
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
1 row in set (0.00 sec)
```

__**Solution: Rebuild slave**__

__On Master__

- Login to mysql

```bash
mysql -u root -p (you will be prompted for password)
```

- Check master status

```bash
mysql> show master status\G
*************************** 1. row ***************************
             File: mysql-bin.000044
         Position: 553540493
     Binlog_Do_DB:
Binlog_Ignore_DB:
Executed_Gtid_Set:
1 row in set (0.00 sec)
```

- Reset master

```bash
mysql> reset master;
Query OK, 0 rows affected (1.40 sec)
```

- Check master status again

```bash
mysql> show master status\G
*************************** 1. row ***************************
             File: mysql-bin.000001
         Position: 13766
     Binlog_Do_DB:
Binlog_Ignore_DB:
Executed_Gtid_Set:
1 row in set (0.00 sec)
```

- Exit from mysql prompt and start taking dump of master. This will take some time depending on size of database

```bash
bring@mysql2qa:~$ mysqldump -uroot -p --all-databases --master-data=1 --single-transaction --quick  > /tmp/dbdump.db
Enter password:
```

- Once completed, check the file to be sure

```bash
ls -ltrh
-rw-r--r--  1 bring    deploy   2.0G Mar 29 10:03 dbdump.db
```

__**Done with steps on master, now login to slave**__

- SCP dump file from master to slave

```bash
scp user@exdap211:/var/db/backup/dbdump.db /var/db/backup/dbdump.db
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'exdap211,139.114.173.205' (RSA) to the list of known hosts.
dbdump.db                                                                                                                                       100% 1976MB 164.7MB/s   00:12
```

- Check the file to be sure

```bash
-rw-r--r--  1 bring    deploy   2.0G Mar 29 10:08 dbdump.db
```

- Now login to mysql, stop slave and then reset slave and then exit

```bash
mysql> stop slave;
Query OK, 0 rows affected (0.00 sec)
```

```bash
mysql> reset slave;
Query OK, 0 rows affected (0.25 sec)
```

```bash
mysql> show slave status \G
*************************** 1. row ***************************
               Slave_IO_State:
                  Master_Host: exdap211.xyz.in
                  Master_User: slave_sec
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File:
          Read_Master_Log_Pos: 4
               Relay_Log_File: mysqld-relay-bin.000001
                Relay_Log_Pos: 4
        Relay_Master_Log_File:
             Slave_IO_Running: No
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 0
              Relay_Log_Space: 143
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: NULL
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 100412
                  Master_UUID: f20637be-3530-11e5-a860-005056a05b60
             Master_Info_File: /var/db/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State:
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
1 row in set (0.00 sec)
mysql> exit
```

- Rebuild slave from dump, this will again take some time

```bash
bring@mysql1qa:/tmp$ mysql -uroot -p < /tmp/dbdump.db
Enter password:
```

- Now login to mysql again and start slave and check status

```bash
mysql> start slave;
Query OK, 0 rows affected (0.01 sec)
```

```bash
mysql> show slave status \G
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: exdap211.xyz.in
                  Master_User: slave_sec
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 62088
               Relay_Log_File: mysqld-relay-bin.000003
                Relay_Log_Pos: 62251
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 0
                   Last_Error:
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 62088
              Relay_Log_Space: 62425
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 0
Master_SSL_Verify_Server_Cert: No
                Last_IO_Errno: 0
                Last_IO_Error:
               Last_SQL_Errno: 0
               Last_SQL_Error:
  Replicate_Ignore_Server_Ids:
             Master_Server_Id: 100412
                  Master_UUID: f20637be-3530-11e5-a860-005056a05b60
             Master_Info_File: /var/db/mysql/master.info
                    SQL_Delay: 0
          SQL_Remaining_Delay: NULL
      Slave_SQL_Running_State: Slave has read all relay log; waiting for the slave I/O thread to update it
           Master_Retry_Count: 86400
                  Master_Bind:
      Last_IO_Error_Timestamp:
     Last_SQL_Error_Timestamp:
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
1 row in set (0.00 sec)
```


