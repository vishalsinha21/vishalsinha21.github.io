---
published: true
title: Resolve Mysql Replication Issues - Duplicate Entry
layout: post
---
These steps would help in resolving Mysql database replication issues arising out of duplicate entry in slave database. 

**Step-by-step guide**

- SSH to slave DB system. Example: `ssh <user>@<server>` (could be different server)

- Connect to mysql. Example: `mysql -u root -p`. Note: It will prompt for password.

- Check slave status. Example: `show slave status\G`. You might see something like this

```
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: <master-server.com>
                  Master_User: slave_sec
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000038
          Read_Master_Log_Pos: 865774015
               Relay_Log_File: mysqld-relay-bin.000021
                Relay_Log_Pos: 25092925
        Relay_Master_Log_File: mysql-bin.000038
             Slave_IO_Running: Yes
            Slave_SQL_Running: No
              Replicate_Do_DB:
          Replicate_Ignore_DB:
           Replicate_Do_Table:
       Replicate_Ignore_Table:
      Replicate_Wild_Do_Table:
  Replicate_Wild_Ignore_Table:
                   Last_Errno: 1062
                   Last_Error: Error 'Duplicate entry '6213' for key 'PRIMARY'' on query. Default database: 'testdb'. Query: 'insert into orders (created_at, user_id, receiver_id) values ('2016-01-12 08:48:09', null, null)'
                 Skip_Counter: 0
          Exec_Master_Log_Pos: 817513358
              Relay_Log_Space: 73353756
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
               Last_SQL_Errno: 1062
               Last_SQL_Error: Error 'Duplicate entry '6213' for key 'PRIMARY'' on query. Default database: 'testdb'. Query: 'insert into orders (created_at, user_id, receiver_id) values ('2016-01-12 08:48:09', null, null)'
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
     Last_SQL_Error_Timestamp: 160113 07:58:25
               Master_SSL_Crl:
           Master_SSL_Crlpath:
           Retrieved_Gtid_Set:
            Executed_Gtid_Set:
                Auto_Position: 0
1 row in set (0.00 sec)
```

Note: if slave status returns nothing `Empty set (0.00 sec)` then you might be on master.

- Better resolve this and don’t skip as suggested at many places as your slave can go out of sync with master and create problems in future.
This might be happening because some application might be pointing to slave DB instead of master, so best practice is to have slave as read only so that applications do not accidentally write to slave DB. Try to locate duplicate entry and delete that entry from slave DB.

- Once you have deleted the old entry then execute `stop slave` and then `start slave` on slave DB.

- Most probably replication will start again and come back to normal. If it gets stuck again for same error for some other record then repeat same steps. In case there are many such entries then better to reset slave and start replication again. Check slave status again to confirm.

```
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: <master-server.com>
                  Master_User: slave_sec
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000038
          Read_Master_Log_Pos: 867336894
               Relay_Log_File: mysqld-relay-bin.000021
                Relay_Log_Pos: 57402302
        Relay_Master_Log_File: mysql-bin.000038
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
          Exec_Master_Log_Pos: 849822735
              Relay_Log_Space: 74916972
              Until_Condition: None
               Until_Log_File:
                Until_Log_Pos: 0
           Master_SSL_Allowed: No
           Master_SSL_CA_File:
           Master_SSL_CA_Path:
              Master_SSL_Cert:
            Master_SSL_Cipher:
               Master_SSL_Key:
        Seconds_Behind_Master: 46579
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
      Slave_SQL_Running_State: Reading event from the relay log
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
