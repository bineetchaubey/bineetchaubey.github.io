# Flume install  guide

----- copy the data from any source to destination in realtime.

from flume installation file ==  follow step 


copy apache_flume tar zip file in hadoop installation diretory 
 
 my  system install directory is /usr/local/work/

     $> cp /home/hadoop/Desktop/sf_hadoop_packages/apache-flume-1.5.0-bin.tar.gz  /usr/local/work/
     $> cd /usr/local/work/

==== untar file

    $>   tar xvzf apache-flume-1.5.0-bin.tar.gz
 rename untar apache-flume-1.5.0-bin file to flume
    $>  mv apache-flume-1.5.0-bin flume
 

update  bashrc file

      $>  nano ~/.bashrc
      
update following content
----------------------------------------

JAVA_HOME=/usr/local/work/java

JAVA_OPTS="-Xms100m -Xmx200m -Dcom.sun.management.jmxremote"

    $> source ~/.bashrc


now  make an agent 

== Copy the config file in Flume conf folder to change for custom agents

    $ cd /usr/local/work/flume
    
 make flume config file
 
     $ cp conf/flume-conf.properties.template    conf/flume.conf
    
make flume invironement  file

     $ cp conf/flume-env.sh.template    conf/flume-env.sh

== Open flume-env.sh and configure JAVA_HOME

     $ nano conf/flume-env.sh

update below:

JAVA_HOME=/usr/local/work/java

JAVA_OPTS="-Xms100m -Xmx200m -Dcom.sun.management.jmxremote"


== Modify the flume.conf in conf directory and add following to it. Also comment the existing properties.


agent.sources = tail-source
agent.channels = memoryChannel
agent.sinks = log-sink    hdfs-sink

###### For each one of the sources, the type is defined
agent.sources.tail-source.type = exec

agent.sources.tail-source.command = tail -F /home/hadoop/flume_data/amit.txt


###### The channel can be defined as follows.

agent.sources.tail-source.channels = memoryChannel


###### Each sink's type must be defined

agent.sinks.log-sink.type = logger


###### Specify the channel the sink should use

agent.sinks.log-sink.channel = memoryChannel


agent.sinks.hdfs-sink.channel = memoryChannel

agent.sinks.hdfs-sink.type = hdfs

agent.sinks.hdfs-sink.hdfs.path = hdfs://localhost:8020/user/hadoop/flumedata/

agent.sinks.hdfs-sink.hdfs.fileType = DataStream



###### Each channel's type is defined.

agent.channels.memoryChannel.type = memory

agent.channels.memoryChannel.capacity = 100


===============

note -  above cofig file 

  we create a custom agent which name is agent, we can set any agent name then we can define property for that agent 
  ex.
  sources
  channel
  sink
  etc
 each property have define opration, need to read from internet
    

   * agent.sources.tail-source.command this property define from where data is pull, ie it is source location
   
   * agent.sinks.hdfs-sink.hdfs.path -:  this is define detintion loaction where flume data push data ie store data. 

now testing time 

--- Create a folder into local system in /home/hadoop/


     $  mkdir /home/hadoop/flume_data
     
     $  cd /home/hadoop/flume_data/

---- create  blank file --

    $  touch amit.txt
    
    $  cat amit.txt 

 now open a new terminal  and excute follwoing commond

     $> flume-ng agent -n  agent -f /usr/local/work/flume/conf/flume.conf -D flume.root.logger=DEBUG,console 


now open second terminal and write follwoing taxt and push in previous created file amit.txt

     $  echo "this is my first flume agent" >> amit.txt

 now see first terminal in which agent is excuting process  , you will see data will stream into HDFS loation or location with is define in flume config file.




==================================================

Create  a new config agent  kmplus  in usr/local/work/flume/conf/flume.conf

kmplus.sources = tail-source
kmplus.channels = memoryChannel
kmplus.sinks = log-sink    hdfs-sink

###### For each one of the sources, the type is defined
kmplus.sources.tail-source.type = exec
kmplus.sources.tail-source.command = tail -F /home/hadoop/flume_data/km_flume.txt

###### The channel can be defined as follows.
kmplus.sources.tail-source.channels = memoryChannel

###### Each sink's type must be defined
kmplus.sinks.log-sink.type = logger

######Specify the channel the sink should use
kmplus.sinks.log-sink.channel = memoryChannel

kmplus.sinks.hdfs-sink.channel = memoryChannel

kmplus.sinks.hdfs-sink.type = hdfs

kmplus.sinks.hdfs-sink.hdfs.path = hdfs://localhost:8020/user/hadoop/flumedata2/

kmplus.sinks.hdfs-sink.hdfs.fileType = DataStream



###### Each channel's type is defined.
kmplus.channels.memoryChannel.type = memory

kmplus.channels.memoryChannel.capacity = 100



in this file source loaction is /home/hadoop/flume_data/km_flume.txt

and destination is hdfs://localhost:8020/user/hadoop/flumedata2/

now we have steam data in realtime in destination directory from source to destination 

we have create a file in local system on locatio /home/hadoop/flume_data/km_flume.txt

and we have push data in /home/hadoop/flume_data/km_flume.txt file with commond like

     $ touch /home/hadoop/flume_data/km_flume.txt;

     $ cat "1|bineet kumar chaubey|22|google|hr" >> /home/hadoop/flume_data/km_flume.txt

now start flume agent kmplus  with terminal 

     $ flume-ng agent -n kmplus -f /usr/local/work/flume/conf/flume.conf -D flume.root.logger=DEBUG,console


and push data in km_flume.txt file

     $ cat "1|bineet kumar chaubey|22|google|hr" >> /home/hadoop/flume_data/km_flume.txt
     $ cat "1|bineet kumar chaubey|22|google|hr" >> /home/hadoop/flume_data/km_flume.txt
     $ cat "1|bineet kumar chaubey|22|google|hr" >> /home/hadoop/flume_data/km_flume.txt


///  now make a extenal hive table and process data with hive table

     hive> create external table kmplus_flume (id int,name string,age int, company string , dept string ) row format delimited fields terminated by '|' location '/user/hadoop/flumedata2/';

     hive> select * from kmplus_flume;











