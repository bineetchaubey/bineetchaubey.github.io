commod- 

open terminal   ctrl+alt + t

why it is secure 

=================07/10/2017=====================

for namenode/ hdfshealth
https://loclahost:50070
  

for yarn-uI 

http://localhost:8088


create a diretory
/user/hadoop

=== create a diretory in hdfs system ====

$ hadoop fs -mkdir -p /user/hadoop

======   copy from local to hdfc system  ==== 
$ hadoop fs -copyFromLocal /home/hadoop/file.txt /user/hadoop

====  copy from hdfc system to local======  
$ hadoop fs -copyToLocal /user/hadoop/file.txt /home/hadoop/file_hdfs.txt


===  copy file from hdfs source to hdfs destination   ====
 
$ hadoop fs -mkdir   /user/hadoop/input_data    //  {make a new directory input_data }

$ hadoop fs -cp  /user/hadoop/file.txt  /user/hadoop/input_data/file.txt


hadoop fs -rm /user/hadoop/file.txt

Note: --- 
-r remove every thing 
 and -R remove only remove safe file and ask for readony file 

======= for help hadoop commond ==
$ hadoop fs -help <commond name>

$ hadoop fs -help cp
$ hadoop fs -help mkdir 

===== generte public key ===

$ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa

cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys    //  >>  append at last in file while  >  replace content in file


------diff rsa vs dsa ---  Need to know  

//  install mysql server 
$ sudo apt-get install  mysql-server 

================================
====================== =====================
====================   =================
================== hive ======================
#############################################

hive is a warehouse --


install hive froma a tar.gz package  


copy packeg into hadoop directory ====
cp Desktop/sf_hadoop_packages/apache-hive-0.13.1-bin.tar.gz  /usr/local/work/

move hdoop install directriy location 

 $ cd /usr/local/work/
== Untar tar.gz fiel.

$ tar xvzf apache-hive-0.13.1-bin.tar.gz 

$ ls -l

====== Rename apache-hive-0.13.1-bin  to hive == 
 $ mv  apache-hive-0.13.1-bin hive

========  update .bashrc  file ======

 $ nano ~/.bashrc

      export HIVE_PATH = /usr/local/work/hive
      export PATH = $PATH:$HIVE_HOME/bin

======= rebuild .bashrc file   ========== 
  $ source ~/.bashrc

====== check if hive install ===
 $ hive
;ljkwwwwo8m aaaaaa	
>hive


---- create a table employee in hive ----  

$ create table employee (id int, name string,dept string,sal int) row format delimited fields terminated by '|';

-----  load data into hive table from local file system ------

$ load data local InPath '/home/hadoop/file.txt' into table employee;   // file.txt previousaly created file.txt on local system.

---- see table schema ------ 

$ describe employee;

---- select data from --- employee table 

$ select * from employee;

---- put fille from local to haddop system 

$ hadoop fs -put /home/hadoop/file2.txt /user/hadoop

//  load data inot hive table from hadoop eco-system 

$ load data InPath '/user/hadoop/file.txt' into table employee;

above commond  only  store file in hive warehouse system , and file schema will match at time for fetch records. 
we can change hive table schema at any time. 

-- data type mis-match  give error while schema give null value.


note-: hive make metastore_db folder  in current working directory.

if you acccess hive on other location then . previously created table will not show at new location

this is drawback in hive / so we store mysql database to store metadata for hive table for consistency



$ show tables; 
$  describe formatted employee;

$ describe extended employee;
$ truncate table employee;
$ drop table employee;

$ create database sample;
$ use sample;

+++++++++++++++++++++  matedata of hive store in 'derby' database default

============================  home work =======


create table emp (id int, fname string,lname string, email string, gender string , address string,salary double, dept string) row format delimited fields terminated by '|';


load data local INPATH '/home/hadoop/Downloads/emp.txt' into table emp;




==========================================================
==========================================================
========================14/10/2017=========================
===========================================================

mysql -u root -p
mysql> CREATE DATABASE metastore_db;
mysql> use metastore_db;

mysql> SOURCE /usr/local/work/hive/scripts/metastore/upgrade/mysql/hive-schema-0.13.0.mysql.sql;
mysql> CREATE USER 'hadoop'@'localhost' IDENTIFIED BY 'hadoop';
mysql> GRANT all on metastore_db.* to 'hadoop'@localhost identified by 'hadoop';
mysql> flush privileges;
mysql> exit;


Step9: Change configuration of Hive
$ cd /usr/local/work/hive/conf
$ sudo cp hive-default.xml.template hive-site.xml
$ sudo gedit hive-site.xml
change the below properties (refren with pdf file and change only value property in hive-site.xml )


---- start hadoop if not start $ start-all.sh---

Step10: Creating a Table in Hive
$ hive
hive> Create table sample ( id int, name string);
hive> exit;




Step11: Checking the table schema in mysql db
$ mysql -u hadoop -p
mysql> use metastore_db;
mysql> select * from TBLS;




example/

this commod cretae a table with spedify location. this commong withh create a table and map that table with location directory
and if user will delete table  then files store specify location will not delete;

while normal commond created table, data file will also deleted when user will delete table;

hive > create external table emp_ext (id int,name string,dept string,salary int) row format delimited fields terminated by '|' location '/user/hadoop/input_data/';


15/10/2017

only storage location while /usr/local/work/hadoop is installation direcotry where haddoop,hive etc core file are placed



// hadoop sytem folder directory; 
/user/hadoop/hive 


important 


loading data from table,

>   cretae table emp(id int , name string , dept string, salary int) ...... 

> load data local INPATH ..............

> create table emp_new like emp;

> insert table emp_new select * from emp;
  location of above table will be hive /warehouse;
    file name 00000_0
    part-m-ooooo


> create table emp_new as select * from emp;



partion data store in table




fil1. txt

1|svx|200|2017-10-01
1|sff|763|2017-10-01
1|yrtyrt|763|2017-10-02



fil2. txt

1|svx|200|2017-10-02
1|sff|763|2017-10-02
1|gfr|763|2017-10-02

static partion----



dyanmic partiiton----

note - dyanamic partion only  make with other table 

create table dynamic (int int , name string , salary int, date_today date) prtitioned by (creation_date) row format delimited fields terminated by '|';



SET hive.exec.dynamic.partition = true;
SET hive.exec.dynamic.partition.mode = nonstrict;


>insert into table dynamic_tab partition(creation_date)
  select id, name salry, date_today, date_today as creation from ext_tab;



> hive -e 'query ' >> file.txt;



##########################################
29/10/2017

################################

step  to  cretae a progream for user define function for hive 



cretae a java program  
create a class 
 make a function in validate

test funcaionlaity
add  hive udf pakcage by following step 
1. click program folder

2. buld Path  ->configure buld path -> open a window
 3. slect library tab
    3.2 click  add external jar button - select jar file location where jar package is located and click okay

  create class file and xtend UDF class  now import option avvalable whne mouse over exteds class click import class
     this will display only package or library already added in project

4. now crate a avaluate program - and retrun variable
5. test program own
6. create a jar file 
   6.1 claick project folder- select Export
   6.2 select java folder in next window - jar File -> click next->  provide jar file location (where you want to save) 
    and name of jar file and click finish
    -- jar file will created 
     TODO -------------------
     
     add jar file in hive and use 
     select  functionname<column_name > from table name ;
   
       note- jar -tf jar1.jar  ///  display class name


#########################################33

Custom DATATYPE

1. array();
   1,abc,4000,a&b&c,hyd  
   2,dgc,3484,d&f,bang

hive> create table tab_array (id int, name string, sal bigint, sub array<string>,city string)
row format delimited fields terminated by ','
collection items terminated by '$';

>hive load .....

hiev> select * from  tab_array;


1, abc 4000, ["a","b","c"] hyd
2 dgc 3484 ["d"," f"] bang

hive> select sub[2] from tab_array;

c
NULL



=
=======================================

data in file
1,abc,4000,a&b&c,pf#500$epf#200,hyd,  
2,dgc,3484,d&f,pf#1500,bang


hive> create table tab_map (id int, name string, sal bigint, sub array<string>, dud map<string,int> ,city string)
row format delimited fields terminated by ','
collection items terminated by '$'
map keys terminated by '#';

hive >load data


hive > select * from tab_map;
1 abc 4000, ["a","b","c"] {"pf": 500,"epf": 200} hyd
2 dgc 3484 ["d"," f"] {"pf": 1500} bang

hive > select dud["pf"] from tab_map;
     500
     1500

==============================================

data in file
1,abc,4000,a&b&c,pf#500$epf#200,ap$hyd$500002,  
2,dgc,3484,d&f,pf#1500,bang,kar&bang$600001


hive> create table tab_struct (id int, name string, sal bigint, sub array<string> dud map<string,int> ,addr struct < state:string,city:string,pincode: int>)
row format delimited fields terminated by ','
collection items terminated by '$'
map keys terminated by '#';


hive > load  .....

hive > select * from tab_struct;

1 abc 4000, ["a","b","c"] {"pf": 500,"epf": 200} ["state": ap, "city":hyd,"picode":500002]
2 dgc 3484 ["d"," f"] {"pf": 1500} ["state": kar, "city":bang,"picode":600001]

hive> select addr.city from tab_struct;
hyd
bang



























  

















 




 































































