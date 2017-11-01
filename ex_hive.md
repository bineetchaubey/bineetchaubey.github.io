
# Hive tutorial

###### file1.txt

id | name | sallary | date
-- | ---- | ------- | --
1|abc|23232323|2016-01-01
2|abc|23232323|2016-01-01
3|abc|2334523|2016-01-01
4|abc|23545323|2016-01-01
5|abc|24587323|2016-01-01
6|abc|2334873|2016-01-01

Actual file data 

    1|abc|23232323|2016-01-01
    2|abc|23232323|2016-01-01
    3|abc|2334523|2016-01-01
    4|abc|23545323|2016-01-01
    5|abc|24587323|2016-01-01
    6|abc|2334873|2016-01-01


###### file2.txt

id | name | sallary | date
-- | ---- | ------- | --
11|abc|23232323|2016-01-02
12|xyz|23234323|2016-01-02
13|abd|23234323|2016-01-02
14|abr|22332323|2016-01-02
15|abt|23342343|2016-01-02


Actual data

	11|abc|23232323|2016-01-02
    12|xyz|23234323|2016-01-02
    13|abd|23234323|2016-01-02
    14|abr|22332323|2016-01-02
    15|abt|23342343|2016-01-02
	
Create dynamic table 

    hive> create table ext_tab ( id int, name string, salary int, date_today date ) partitioned by ( creation_date date) row format delimited fields terminated by '|' ; 
    hive>  load data local inpath '/home/hadoop/a/file1.txt'  into table ext_tab partition (creation_date="2016-01-01");
    hive> load data local inpath '/home/hadoop/a/file2.txt'  into table ext_tab partition (creation_date="2016-01-02"); 

#### Dynamic table 

Create a dynamic table  table 

    hive> create table dyn_emp( id int, fname string,lname string,email string,  gender string, address string, salary int, dept string ) partitioned by (dept_dy string) row format delimited fields terminated by '|' ;
    hive> insert into table dyn_emp partition(dept_dy) select id, fname,lname,email,gender,address,salary, dept, dept as dyn_emp from  emp;


 Ex. insert into table tab_test partition(name_par) select id, name, salary, date_today, name as name_par from ext_tab;

  Note :- __partiiton in backbone of hive and it is vary important in company__
  ####### TODO multiple partiiton  on Year month and date



## Objective create a user define function in Hive 

Follow following step to create  custom function in hive

1. Cretae a java program jar file
	1. Create a java program ny creat a new project
	2. Enclude Hive UDF Jar File package
	3. Create a class in that project and extends UDF Class 
	4. Create an __evaluate__ method in this class and define all business rule in this function and return varriable
	5. run and test program
	6. create a jar file for this project and store that jar file in a location /home/hadoop/Desktop/myabc.jar
	
2. Now add above newaly craated jar file inot hive system 
    	hive> add jar /home/hadoop/Desktop/myabc.jar;

3. Create a temporary function 
 
		hive>  create temporary function sample as 'my_abc'   
    
    // while my_abc is class file in jar file with i have addedd 
    
Note- $ jar -tf bac.jar file display all classname 

Now perform query with this function in  
 
    hive>  select my_abc(name) from office limit 10  //  kn is user define custom function which we have with java jar file.



## Custom data type
=======
File - __data_array.txt__

    1,abc,4000,a$b$c,hyd  
    2,dgc,3484,d$f,bang
    3,ght,4354,h$g$d,lukcnow
    4,hjt,9876,k$i$u,delhi

Create table

    hive> create table apple_array(id int,name string, sal bigint, sub array<string>, city string)  row  format delimited fields terminated by ',' collection items terminated by '$';

    hive> load data local INPATH '/home/hadoop/Documents/hadoop- training/example_data_file/data_array.txt' into table apple_array;

	hive> select * from apple_array;    
OK
    
id | name | salary | array | city
-- | -- | --- | -- | --
1 | abc	| 4000 | ["a","b","c"] |	hyd  
2 | dgc	| 3484 | ["d","f"]	| bang
3 |	ght	| 4354 | ["h","g","d"] | lukcnow
4 |	hjt	| 9876 | ["k","i","u"]	| delhi

Time taken: 0.183 seconds, Fetched: 4 row(s)

Find array index valus with followind query

     hive> select sub[2] from apple_array;
OK

c

NULL

d

u

Time taken: 19.651 seconds, Fetched: 4 row(s)


====================
### Map data type array 
====================

File Name:- __apppl_map.txt__

    1,abc,4000,pf#699$epf:600,hyd
    2,dgc,3484,pf#699$epf:700,bang
    3,ght,4354,epf:600,lukcnow
    4,hjt,9876,pf#562,delhi


Create table 

    hive> create table apple_map (id int, name string,sal bigint, dud map <string,int>,city string) row format delimited fields terminated by ',' collection items terminated by '$' map keys terminated by '#';

Load data into table  // file data as apppl_map.txt
		
	hive> load data local INPATH '/home/hadoop/Documents/hadoop-training/example_data_file/apple_map.txt' into table apple_map;

	hive> select * from apple_map 
                                                                                          
OK
id | name | salary | map_array | city
-- | -- | --- | -- | --
1	| abc |	4000 |	{"pf":699,"epf":600} |	hyd  
2	| dgc |	3484 |	{"pf":699,"epf":700} |	bang
3	| ght |	4354 | 	{"epf":600}	| lukcnow
4	| hjt |	9876 | 	{"pf":562}	| delhi

fetch spacial map key data 

hive> select dud["pf"] from apple_map;
Total MapReduce CPU Time Spent: 1 seconds 440 msec
OK
699
699
NULL
562
Time taken: 28.738 seconds, Fetched: 4 row(s)


=============================
### Struct data type 
============================
apple_struct
file-name - apple_struct.txt
1,abc,4000,pf#699$epf#600,ap$hyd$500002
2,dgc,3484,pf#699$epf#700,kar$bang$50000763 
3,ght,4354,epf#600,utterpredesh$lucknow$500763
4,hjt,9876,pf#562,delhi$delhi$506232

Cretae table for struct type data

		hive>create  table apple_struct (id int, name string,sal bigint,dud map<string,int>, addr struct<state:string,city:string,pincode:int>)
		row format delimited fields terminated by ','
		collection items terminated by '$'
		map keys terminated by '#';

Load data form file into table 

	hive> load data local INPATH '/home/hadoop/Documents/hadoop-training/example_data_file/apple_struct.txt' into table apple_struct;

Fetch records form table	
	
	hive> select * from apple_struct;

OK

id | name | salary | map_array | address
-- | -- | --- | -- | --	
1 |	abc	| 4000 | {"pf":699,"epf":600}|{"state":"ap","city":"hyd","pincode":500002}
2 | dgc	| 3484 | {"pf":699,"epf":700} |	{"state":"kar","city":"bang","pincode":null}
3 |	ght	| 4354 | {"epf":600} | 	{"state":"utterpredesh","city":"lucknow","pincode":500763}
4 |	hjt	| 9876 | {"pf":562}	| {"state":"delhi","city":"delhi","pincode":506232}

Time taken: 0.404 seconds, Fetched: 4 row(s)

select pertucule key in struct type data 
    
	hive> select addr.city from apple_struct;

Total MapReduce CPU Time Spent: 1 seconds 70 msec

OK

hyd

bang

lucknow

delhi

Time taken: 32.515 seconds, Fetched: 4 row(s)

==========================================
  #### Prectical of join 

table -
 * customer
 * orders

##### Create customer table

	hive> create table customer(id int, name string, age int, address string, sal string) row format delimited fields terminated by '|'

load data in table 
	
	hive> load data local INPATH '/home/hadoop/Documents/hadoop-training/example_data_file/customer.txt' into tables customer;

##### Create order table

	hive> create table orders (id int, order_date date,cust_id int,amount double) row format delimited fields  terminated by '|';
load data in to table
	
     hive>create data local INPATH '/home/hadoop/Documents/hadoop-training/example_data_file/customer.txt' in to table orders;
__changes column schenma__
	hive> alter table orders change order_date order_date timestamp;

inner join query 
	
	hive> SELECT c.id, c.name, c.age, o.amount FROM CUSTOMER c JOIN ORDERs o ON (c.id = o.cust_id);
hive> SELECT c.id, c.name, o.amount, o.order_date FROM CUSTOMER c  LEFT OUTER JOIN ORDERS o ON (c.id = o.cust_id);

Right outer join query

     hive > SELECT c.id, c.name, o.amount, o.order_date FROM CUSTOMER c RIGHT OUTER JOIN ORDERS o ON (c.id = o.cust_id);

FUll outer join query
	 
    hive>  SELECT c.id, c.name, o.amount, o.order_date  FROM CUSTOMER c FULL OUTER JOIN ORDERS o ON (c.id = o.cust_id);


#### Static partion on external location


1. Create a dirctory in hadoop system  "static_ext_p" 	 	
  1.1 Create two sub directrory in static_ext_p  
    *  creation_date=2016-01-01
    *  creation_date=2016-01-02

Copy file in above directory create an table with external loction

	$> hadoop fs -put  /home/hadoop/Documents/hadoop-training/example_data_file/date1.txt  /user/hadoop/static_ext_p/creation_date=2016-01-01/

    $> hadoop fs -put  /home/hadoop/Documents/hadoop-training/example_data_file/date2.txt  /user/hadoop/static_ext_p/creation_date=2016-01-02/

Create table with external location with partiiton option 
	
    hive> create external table static_external ( id int, name string, salary int, date_today date ) partitioned by ( creation_date date) row format delimited fields terminated by '|' location '/user/hadoop/static_ext_p/';

Now  excute query ===

    hive> select * from static_external;

--- no result found because pertion is not created;

Now create partion  with alter table;

	hive> alter table static_external add partition(creation_date="2016-01-01"); 

Now fetch data form table
	
	hive> select * from static_external;
Return some data

Add some other partion by alter table 
	
    hive> alter table static_external add partition(creation_date="2016-01-02");




