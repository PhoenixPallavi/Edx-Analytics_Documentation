Connecting Hive with JAVA
==========================

We can run Hive queries from a Java Database Connectivity (JDBC)  Connectivity (ODBC) application using  the Hive JDBC  driver. The Hive JDBC driver allows you to access Hive from a Java program  that uses JDBC to communicate with database products. 

Hive provides a Type 4 (pure Java) JDBC driver, defined in the class an application that can connect to a Hive server using the Hive JDBC driver  org.apache.hadoop.hive.jdbc.HiveDriver. When configured with a JDBC URI of the form jdbc:hive://host:port/dbname, a Java application can connect to a Hive server running at the specifed host and port. The driver makes calls to an interface implemented by the Hive Thrift Client using the Java Thrift bindings. 

Steps to connect to hive
------------------------

| 1.Start the hadoop  cluster using steps described previously.
| 2.Start the hive server at port number 10,000 which is the default port number for hive service.
  The command is 
 $hive –service hiveserver 
| 3.Create a java class to connect to the hive driver containing the following jar files:-
    
* apache-logging-log4j.jar
* commons-httpclient-3.0.1.jar
* commons-logging-1.1.3.jar
* hadoop-core-1.2.0.jar
* hive-cli-0.13.0.jar
* hive-common-0.13.0.jar
* hive-exec-0.13.0.jar
* hive-jdbc-0.13.0.jar
* hive-metastore-0.13.0.jar
* hive-service-0.13.0.jar
* hive.txt
* libfb303-0.9.0.jar
* libthrift-0.9.0.jar
* log4j-1.2.16.jar
* slf4j-api-1.7.7.jar
* slf4j-jdk14-1.7.7.jar


Following is a snippet of the code:-::

   import java.sql.Connection;
   import java.sql.DriverManager;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.SQLException;
   import java.sql.Statement;
   import java.util.ArrayList;
   import java.util.Collections;
   import java.util.Comparator;
   import java.util.LinkedList;
   import java.util.List;
   import java.util.Queue;
   
   public class Connect 
   {
     public Connect()
     {
     }
     static public Connection GetConnection(){
   	  try
   	  {
   		  Class.forName("org.apache.hadoop.hive.jdbc.HiveDriver");
   		  Connection connect = DriverManager.getConnection("jdbc:hive://localhost:10000/default", "", "");
   			 System.out.println("hoho");
   			 /*
   		 Class.forName("org.apache.hadoop.hive.jdbc.HiveDriver");
   		 Connection connect = DriverManager.getConnection("jdbc:mysql://localhost:3306/expt_old","root","svsj");
   		 */return(connect);
   		   
   	  }
   	  catch(Exception e)
   	  {
   		  System.out.println("exception caught");
   		  return null;
   	  }
   
     }
   }

| 4.Run the java program.



Error Points
-------------


* hive --service hiveserver 
 |Starting Hive Thrift Server org.apache.thrift.transport.TTransportException: Could not create ServerSocket on address 0.0.0.0/0.0.0.0:10000.
 |solution :-   
 | 
 |1.Set the port number   using :-
 |     $export HIVE_PORT=10000
 |2.Check which service is running on port 10000
 |   $sudo lsof -i -P | grep “listen”|grep “10000”
 |3.If there is a process runnign on that port , kill it
 |  
 |  $ kill -p pid
 |
 |4.Start the hive server using the same command 
 |   $ hive --service hiveserver 
 |
 |
* Response given by hive server while starting and after getting started is :
 |  Starting hive server
 |
 |Dont get confuse , even after getting started it does not specify that it has started and gets stuck with this line.
 |So after you get this line , move forward and run the hive program.

