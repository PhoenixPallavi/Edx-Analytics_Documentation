Machine Learning :
==================

Machine learning, a branch of artificial intelligence, concerns the construction and study of systems that can learn from data. For example, a machine learning system could be trained on email messages to learn to distinguish between spam and non-spam messages. After learning, it can then be used to classify new email messages into spam and non-spam folders.

The main aim of our project is to classify the students as gaming or not gaming. Accordingly, the ITS will intervene the learning process of the gaming students and make their learning process effective. In reference to th research paper, **“Detecting Student Misuse of Intelligent Tutoring Systems” authored by Ryan Shaun Baker, Albert T. Corbett, Kenneth R. Koedinger**, their study says that students who are averted to such 'gaming the system behaviour' (behavior aimed at obtaining correct answers and advancing within the tutoring curriculum by systematically taking advantage of regularities in the software’s feedback and help) learn 2/3rds as much as similar students who do not engage in such behaviors. They came up with a machine-learned latent response model that can identify whether a student is gaming the system or not. Based on these predictions, the tutor can be re-designed for such students and make their learning process effective.

Baker and his colleagues found that a student’s frequency of gaming was strongly negatively correlated with learning. According to them, understanding why students game the system will be essential to deciding how the system should respond. Ultimately, though, whatever remediation approach is chosen, it is likely to have costs as well as benefits. For instance, preventive approaches, such as changing interface widgets to make them more difficult to game or delaying successive levels of help to prevent rapid-fire usage, may reduce gaming, but at the cost of making the tutor more frustrating and less time-efficient for other students. Since many students use help effectively and seldom or never game the system, the costs of using such an approach indiscriminately may be higher than the rewards. Whichever approach we take to remediating gaming the system, the success of that approach is likely to depend on accurately and automatically detecting which students are gaming the system and which are not.


The LRM they suggested, takes 24 features as input or data source and also the predetermined value of the student 'gaming or not' of a training set of 70 students. Then it uses forward selection for model selection and then finally implements iterative gradient descent to find the best model parameters. The best-fitting model had 4 parameters, and no model considered had more than 6 parameters. They also used a cross-validation techninque, LOOCV (Leave One Out Cross Validation). Finally with the ROC (Receiver Operating Characteristic) curve, they classified the student as gaming or not gaming. On this result, they applied the interventions in the ITS.

From our project's point of view, machine leaning system is trained on student's repective 3 features, so as to make it learn distinguish between students who are gaming the system and students who are not gaming the system. After learning, it can be used to classify whether a student is gaming the system or not.

In order to implement the machine learning algorithms on the features extracted by hive queries, we have to convert them into proper form (like numerical values), suitable for implementaion. To acheive this, we have mapped the query results into the following form :

username	feature1	feature2	feature3	result

This data has been stored in the table feature. For each user there is just one entry in this table and the field 'result' stores the precoded data i.e, whether the student is gaming or not.

Feature 1:
---------

This feature of mapping deals with the question solving ability of a person.This feature not only calculates how many questions have been solved by a user in each course, rather it also delas with the difficulty level of each question solved by a user.For this , we have utilised the information from two tables record and difficulty_level:-

:diff_level:
* problem_id
* attempts
* no_of_users
* level
 
:record:
* problem_id
* username
* attempts

From these two tables we decideed upon a numrical value depending upon the extracted information that how many question is solved by each user of how much difficulty.This information is stored in the following intermediate table:-

:assign:
* username
* value

| This value has been calculated on the folloing basis:-
new_value=old_value+summation(difficulty_level/attempts)/summation(no_of_questions)

| For those users, whose entries does not exists in the final table feature whose schema is expalined below,are created with dummy values.And those values are finally upadted.This feature counts in feature1 , so its value us stored in the f1 column against a particular user in the feature table:-

:feature:
* username
* f1
* f2
* f3
* total

| Following is the query for the same ::

	    stmt.executeQuery("insert overwrite table assign select b.username,sum(a.diff*b.attempts)/count(*) from diff a join record b group by username");
	    res=stmt.executeQuery("select * from assign");
		while(res.next())
		{
			//System.out.println(res.getString(1)+"\t"+res.getString(2));//+res.getString(3)+"\t"+res.getString(4)+res.getString(5)+"\t");
		}
	    
	    stmt.executeQuery("insert into table feature select a.username,0,0,0,0 from assign a where not exists(select * from feature where username=a.username)   ");
	   res=stmt.executeQuery("select * from assign");
	    float g;
	  
		while(res.next())
		{
			a=res.getString(1);
			g=res.getFloat(2);
			//System.out.println(res.getString(1)+"\t"+res.getString(2));//+"\t"+res.getString(3)+"\t"+res.getString(4)+"\t"+res.getString(4)+"\t");
		    stmt.executeQuery("insert overwrite  table feature select username,case when username='"+a+"' then "+g+" else f1 end as f1,f2,f3,result from feature ");
		}




Feature 2:
----------


:Mapping the seek_time feature:

We have written a java class Map_feature_seek_time. In this for each user, we have calculated :

[sum{(duration/(duration+seek))*10}]/number of videos seeked

(Say, d = duration and s = seek time.)

i.e, the sum of the fraction (d/(d+s)) multiplied by 10 (so that the range of a student's seek time remains within 10), divided by the total number of videos he/she has seeked.

If the grade is closer to 10 then the student is regular and seeks less else the student is seeking most of the videos.

The code snippet for the above is: ::


   statement.executeQuery("insert into table feature_seek select username,sum((duration/(duration+seek))*10)/count(*) from seek_time_total group by username");
   statement.executeQuery("insert into table feature select a.username,0,0,0,0 from feature_seek a where not exists(select * from feature where username=a.username)   ");


Feature 3:
----------

This feature is depending on the activity level of the user i. e., how much user is interacting with the system. It combines the results obtained after processing the log file and storing activity of user per day in table activity_per_day into a single value for per user. It is clear that students not interested in the course will have minimum activity level. also students who are trying to game the system will have high activity levels as they will constantly seek, pause videos frequently and while test they will answer the questions without contemplating over the questions.

The  java class written for this purpose is Feature_seek_time. The following snippet fulfills the above described functionality :- ::

   stmt.executeQuery("insert into table feature select apd.username,apd.course_id,0,0,0,0 from activity_per_day as apd where not exists (select username,course from feature as fe where fe.username=apd.username and fe.course=apd.course_id)");
   stmt.executeQuery("insert overwrite table tmp_feature_attempt select username,course_id,(10-abs((sum(video_act)-"+avg+")/("+avg+"*count(*))*10)) as value from activity_per_day group by username,course_id");
   stmt.executeQuery("insert overwrite table feature select f.username,f.course,f.f1,f.f2,case when f.username=apd.username and f.course=apd.course_id then apd.value else f.f3 end as f3,result from tmp_feature_attempt as apd join feature as f on apd.username=f.username and apd.course_id=f.course");

This is incremental query i.e., this will only process the log entries which were not processed earlier. To accomplish task of extracting feature three steps are required. First involves  inserting dummy entries for the entries which were added newly in the log table.Second step involves calculating level of activity and storing the values of activity level in the intermediate table. Value of activity is calculated such that users having level of activity at average level of all the users will be awarded highest score i.e., 10 and as students activity level deviate from the average value of activity level of all the users their score will decrease till the lowest possible score 10. Third step involved in which the scores which were calculated for each students will now be added into the table feature.



