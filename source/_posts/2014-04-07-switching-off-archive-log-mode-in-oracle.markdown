---
layout: post
title: "Switching off Archive Log mode in Oracle"
date: 2011-02-24 22:10:11 +1000
comments: true
categories: oracle
---
Archive Log mode can have a large impact on the performance of your Oracle database since it logs every single change to the data. If you don’t require this level of rollback then here are the commands to switch it off:

1. Login to sqlplus – sqlplus / as sysdba
2. Check your current log mode to make sure you are using archive logging – select log_mode from v$database;
3. Shutdown the database – shutdown immediate;
4. Mount the database but do not start it for operation - startup mount;
5. Set noarchivelog mode - alter database noarchivelog;
6. Open the database for normal operation - alter database open;

Done.