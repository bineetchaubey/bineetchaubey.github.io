 #  Sqoop    data strimming tools  
=================================

####  installation 

 copy scoop zip tar  file  in to /usr/local/work
 
    $ cp /home/hadoop/Desktop/sf_hadoop_packages/sqoop-1.4.5.bin__hadoop-2.0.4-alpha.tar.gz   /usr/local/work/
    $ cd /usr/local/work/
 untar zip tar  file  in usr/loal/work
 
    $ tar xvzf sqoop-1.4.5.bin__hadoop-2.0.4-alpha.tar.gz 
    $ ls -l
   

 rename sqoop  to sqoop
 
   $  mv sqoop-1.4.5.bin__hadoop-2.0.4-alpha sqoop

 update ~/.bashrc  file

    $ nano ~/.bashrc file
   
   export SQOOP_HOME = /usr/local/work/sqoop
   export PATH =  $PATH:$SQOOP_HOME/bin

  
 
#### rebuild bashrc 

   $ source ~/.bashrc



 copy mysql java connecter in to sqoop lib foolder

   $ cp /home/hadoop/Desktop/sf_hadoop_packages/mysql-connector-java-5.1.34.jar /usr/local/work/sqoop/lib


============================================================
commond hostory

    cp /home/hadoop/Desktop/sf_hadoop_packages/sqoop-1.4.5.bin__hadoop-2.0.4-alpha.tar.gz   /usr/local/work/
     cd /usr/local/work/
    ls -l
    mv sqoop-1.4.5.bin__hadoop-2.0.4-alpha.tar.gz  sqoop
     ls -l
    nano ~/.bashrc
     source ~/.bashrc
     cp /home/hadoop/Desktop/sf_hadoop_packages/mysql-connector-java-5.1.34.jar /usr/local/work/sqoop/lib
    ls -l /usr/local/work/sqoop 
     cd  /usr/local/work/sqoop 
     ls-l
    ls -l
    rm sqoop 
    ls -l
    cp /home/hadoop/Desktop/sf_hadoop_packages/sqoop-1.4.5.bin__hadoop-2.0.4-alpha.tar.gz   /usr/local/work/
    tar xvzf sqoop-1.4.5.bin__hadoop-2.0.4-alpha.tar.gz 
    ls -l
    mv sqoop-1.4.5.bin__hadoop-2.0.4-alpha sqoop
    ls -l
    ls -l sqoop
    cp /home/hadoop/Desktop/sf_hadoop_packages/mysql-connector-java-5.1.34.jar /usr/local/work/sqoop/lib
    nano ~/.bashrc
    source ~/.bashrc
    
==================================================================
now time to hand durty with code

Now create table to 

    mysql > create table mysql_test (id int(10), name varchar(55) , PRIMARY KEY(id));
    mysql > insert into mysql_test values (1, 'amit');
    mysql > insert into mysql_test values (2, 'kumar');
    mysql > insert into mysql_test values (3, 'singh');
    mysql > insert into mysql_test values (4, 'anand');
    mysql > insert into mysql_test values (5, 'pandey');
    mysql> select * from mysql_test;


====================================

    $ sqoop help
    $ sqoop list-databases --help
listing database
    $ sqoop list-databases -connect jdbc:mysql://localhost:3306/ -username root -password mysql
    
listing table

    $ sqoop list-tables -connect jdbc:mysql://localhost:3306/croma -username root -password mysql


Import table in to HDFS System

     $ sqoop import -connect jdbc:mysql://localhost:3306/croma -username root -password mysql --table mysql_test -m 1 --target-dir /user/hadoop/sqoop_output/ 

     $ sqoop import -connect jdbc:mysql://localhost:3306/croma -username root -password --table mysql-test -m 1 --target-dir /user/hadoop/sqoop_output

> note---  -m option is mapper // place output  file ie how many file  in output file

##### import data with condition  into HDFS  --where "id=1"

     $  sqoop import -connect jdbc:mysql://localhost:3306/croma -username root -password mysql --table mysql_test --where "id=1" -target-dir /user/hadoop/sqoop_output1/


================= important query == 

    $ sqoop import -connect jdbc:mysql://localhost:3306/croma -username root -password mysql --query "select id, name from mysql_test WHERE id=1 AND \$CONDITIONS" --split-by 'id' --target-dir /user/hadoop/sqoop_output3/

> IMP--  Load INTO Hive ----===========================



==================================================

   mysql table to ------ dump data in hdfs
  

====================================
      
FROM HDFS  TO MYSQL 

11-11-2017
====================================
Now its time to Import data from HDFS to MYSQL

  create  table with same schema as in HDFS
  
     mysql >  create table stock_sqoop (id int(10), ex_change varchar(55) ,stockname varchar(255),sector varchar(255),country varchar(255),stock_date date, open int(11), high int(10), low int(10), close int(11),valume int(11), adj_close int(11), PRIMARY KEY(id));

*** create table stock_sqoop2 (id int(10), ex_change varchar(55) ,stockname varchar(255),sector varchar(255),country varchar(255),stock_date date, open float, high float, low float, close float,valume float, adj_close float, PRIMARY KEY(id));

hive commond -----  describe stock hive table and find hive table location

      hive> describe formatted  stock;
column| data-type
------|-------
id|  int                 	                    
ex_change |         	string              	                    
stockname |        	string              	                    
sector     |        	string              	                    
country   |         	string              	                    
stock_date|         	date                	                    
open     |          	int                 	                    
high      |         	int                 	                    
low       |         	int                 	                    
close    |          	int                 	                    
volume   |          	int                 	                    
adj_close|         	int 


mysql commond ===============

      mysql> describe stock_sqoop;

modify table schema in mysql --- bacuse stock_date  column schema is not matching so we need to change it

      mysql> alter table stock_sqoop MODIFY COLUMN stock_date timestamp;

sqoop commond from hive HDFS to mysql =================


       $ sqoop export --connect jdbc:mysql://localhost:3306/croma --table stock_sqoop  --export-dir /user/hadoop/hive/warehouse/stock.db/stock --username root --password mysql -m 1 --input-fields-terminated-by '\001'

hive table location in HDFS-:     /user/hadoop/hive/warehouse/stock.db/stock

--check mysql if file import into mysql

       mysql> select count(*) from stock_sqoop
















