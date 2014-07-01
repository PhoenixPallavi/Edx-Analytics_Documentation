=======================
**Overall description**
=======================

EdX-data analyzer uses data genreated by edX  in the form of log entries and the database it creates. Data containing information related to the students is stored in the database 'edxapp'. Data from  edxapp is used for analysis purposes like number of dropouts according to education level, location, gender, number of students according to their eduation level enrolled in a particular course.
Log entries genrated by the server will help us to find if the student is gaming the system or not. EdX-data analyzer will constantly look for the new entries  in the log files and parse, process  them to find wheather students are gaming or not. EdX-data analyzer parses the log file entries and stores them using hive on hadoop distributed file system. 

=====================
**Product Functions**
=====================

EdX-data analyzer serves main purpose of determining wheather the student is gaming the system or not. It can be used to interrupt the student who are tring to game the system and adjust the tutor system such that it will be difficult for the student to game the system.

===============
**Constraints**
===============

Analysing the log data to find whether the student is gaming the system or not involves many constriants like diffculties in predicting the state of mind of person by just looking at it's interaction with the system. It is not possible to determine if a student is sleeping while watching the video or whether a student is paying proper attention. Along with difficulties in predicting the state of mind of the student, it is also not possible to note down each and every action or interaction of the user with his/her system due to privacy policies. Instance of this diffcutly can be a situation for example, a student who pauses the video might be pausing the video and indulging in the other off task behavior or a student might be getting confused while watching the video and now try to understand the concept over the internet. As seen in the example, it is difficult to predict the extact state mind of the student. To determine whether the student is gaming or not, detailed analysis is required. Even if we are able to determine whether the student is gaming or not,  we need to take some action to prevent the student from the gaming. But, question is how to determine which steps should be taken to stop the student from gaming as it will depend on the reason behind the gaming which is a furture part of analysis not covered here.

================================
**Assumptions and Dependancies**
================================

EdX-data analyzer assumes that log entries genrated by the EdX ITS server are error free. As only source of input to EdX-data analyzer is data provided by EdX ITS server. EdX-data analyzer totally depends on the EdX ITS for the data.


======================
**Technologies used:**
======================

1. Hadoop:
----------

Apache Hadoop is an open source software project that enables the distributed processing of large data sets across clusters of commodity servers. It is designed to scale up from a single server to thousands of machines, with a very high degree of fault tolerance. Rather than relying on high-end hardware, the resiliency of these clusters comes from the software’s ability to detect and handle failures at the application layer.

Apache Hadoop has two main subprojects:

MapReduce - The framework that understands and assigns work to the nodes in a cluster.
HDFS - A file system that spans all the nodes in a Hadoop cluster for data storage. It links together the file systems on many local nodes to make them into one big file system. HDFS assumes nodes will fail, so it achieves reliability by replicating data across multiple nodes

2. Hive:
--------


Hive is a runtime Hadoop support structure that allows anyone who is already fluent with SQL (which is commonplace for relational data-base developers) to leverage the Hadoop platform right out of the gate.
Hive allows SQL developers to write Hive Query Language (HQL) statements that are similar to standard SQL statements. HQL is limited in the commands it understands, but it is still useful. HQL statements are broken down by the Hive service into MapReduce jobs and executed across a Hadoop cluster.


3. Sqoop:
---------

Sqoop is a command-line interface application for transferring data between relational databases and Hadoop. It supports incremental loads of a single table or a free form SQL query as well as saved jobs which can be run multiple times to import updates made to a database since the last import. Imports can also be used to populate tables in Hive or HBase. Exports can be used to put data from Hadoop into a relational database.



===========================
**Functional Requirements**
===========================


1. The system shall analyse the data based on various parameters of the student such as location,age group,gender etc.
2. The instructor shall be able to choose the comparison parameters and input valid entries to be queried. The instructor shall be able to input the subject for the data to be queried.
3. The data shall be represented in visual format to be understood by the instructor. The visuals formats may include pie-charts,bar charts, line charts etc.
4. The system shall parse the log data and store the parsed data into relevant event related tables.
5. The system shall extract the relevant and useful data from the parsed data.
6. The system shall tell whether a student is gaming a system or not. The system shall do this after analysing the various actions performed by the student while giving the test.

============================
**Performance Requirements**
============================

1. The edx analytics shall support in courses having large number of students (in thousands). There shall be minimal delay in retrieving the data.
2. The analysis shall be done on the data which has not been processed,i.e,only new data shall be considered for analysis. This would avoid the reading of unneccesary data again and again. This would be called as incremental implementation of queries.

==============================
**Non Functional Requirments**
==============================

1. The visual diagrams displaying the analysis with various parameters of the student shall be in a easy form so as to be understood by each and every instructor including those belonging to non-mathematical back-ground.

