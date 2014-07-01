Feature Extraction:
-------------------

In order to determine if a student is gaming the system or not, we have written some queries on the parsed log data (the database we created for the entries from the log file). The features we came up with are :-

| 1. To calculate the minimum number of attempts a student takes in correctly answering a question
| 2. To calculate the amount of tme a student is seeking a video
| 3. To calculate the difficulty level of each question in every course
| 4. To calculate the activity level of a student per day.


Feature 1
~~~~~~~~~

In order to find the difficulty level of the question, it is required to know in how many minimum number of attempts a student was able to answer the question correctly. Edx allows a user to answer a question any number of times even after the student has already given the correct answer. This is why we need to consider only the first attempt in which the student correctly answered the question. As analyzing the attempts after the user has already correctly answered the question is futile because student already knew the answer and those extra attempts will only mean revision for the student or that the student is just playing around with the system.

The java class written for this purpose is Feature_no_of_attempts. The following snippet fulfills the above described functionality :- ::

   statement.executeQuery("insert overwrite table temp select problem_id,username,min(attempts) from problem_check_server where log_id>0 and log_id<=2000 and correctness='correct'  group by problem_id,username");
			//statement.executeQuery("insert overwrite table temp select problem_id,username,min(attempts) from problem_check_server where log_id>0 and log_id<=1000 and correctness='correct'  group by problem_id,username");
			System.out.println("first and last "+start+" "+end);
			statement.executeQuery("insert into table attempts select p.problem_id,p.username,0 from temp p where not exists(select * from attempts where problem_id=p.problem_id and username=p.username)");
			resultset=statement.executeQuery("select * from temp");
			while(resultset.next())
			{
				a=resultset.getString(1);
				b=resultset.getString(2);
				c=resultset.getInt(3);
				statement.executeQuery("insert overwrite table attempts select problem_id,username,case when problem_id='"+a+"' and username='"+b+"' then "+c+" else attempts end as attempts from attempts");
				System.out.println(resultset.getString(1)+resultset.getString(2)+resultset.getString(3));
			}

Finding the number of minimum attempts per user per question has been done in three steps. This feature has been extracted using incremental approach. First step is to identify the new users from newly generated log entries and store them into temporary table along with the minimum number of attempts per question in the corresponding log entries. Second step is to insert dummy entries into the table attempts for the user not already present in the attempts table. Third step is to insert the values extracted in the temporary table into the attempts table.


Feature 2
~~~~~~~~~

One of those features is to calculate the amount of time a user seeked a video. For this, the details of a video is also required. As such information wasn't provided, a project named 'Download'(package name 'fetch_video_information') was written to extract the details of a video.

The table created for this purpose was 'video_information'. The steps involved in this program are:

1. Fetch the video code from the log parsed database('load_video' table) in the Database class.
2. Pass this video code into the URL (in the Down class): http://gdata.youtube.com/feeds/api/videos/"+video_code+"?v=2&alt=jsonc Example - http://gdata.youtube.com/feeds/api/videos/dXb3Tx8V4hU?v=2&alt=jsonc opens the following :- ::

   {"apiVersion":"2.1","data":{"id":"dXb3Tx8V4hU","uploaded":"2013-02-23T11:16:41.000Z","updated":"2013-02-23T11:16:41.000Z","uploader":"aakashlab","category":"People","title":"Android UI and Layouts part 2","description":"","thumbnail":{"sqDefault":"http://i1.ytimg.com/vi/dXb3Tx8V4hU/default.jpg","hqDefault":"http://i1.ytimg.com/vi/dXb3Tx8V4hU/hqdefault.jpg"},"player":{"default":"http://www.youtube.com/watch?v=dXb3Tx8V4hU&feature=youtube_gdata_player","mobile":"http://m.youtube.com/details?v=dXb3Tx8V4hU"},"content":{"5":"http://www.youtube.com/v/dXb3Tx8V4hU?version=3&f=videos&app=youtube_gdata","1":"rtsp://r2---sn-a5m7zu7z.c.youtube.com/CiILENy73wIaGQkV4hUfT_d2dRMYDSANFEgGUgZ2aWRlb3MM/0/0/0/video.3gp","6":"rtsp://r2---sn-a5m7zu7z.c.youtube.com/CiILENy73wIaGQkV4hUfT_d2dRMYESARFEgGUgZ2aWRlb3MM/0/0/0/video.3gp"},"duration":308,"viewCount":371,"favoriteCount":0,"commentCount":0,"accessControl":{"comment":"allowed","commentVote":"allowed","videoRespond":"moderated","rate":"allowed","embed":"allowed","list":"allowed","autoPlay":"allowed","syndicate":"allowed"}}}

3. The above URL opens a page containing the JSON object about that video. So, next we downloaded this piece of information into a file.

The code snippet for the same looks like : ::
URL url = new URL("http://gdata.youtube.com/feeds/api/videos/"+video_code+"?v=2&alt=jsonc");
	
		Scanner s = new Scanner(url.openStream());
		String line;
		while(s.hasNext())
		{
			line=s.nextLine();
			File file = new File("/home/dell/workspace/Download/src/videoJson.json");
			 
			// if file doesnt exists, then create it
			if (!file.exists())
			{
				file.createNewFile();
			}
 
			FileWriter fw = new FileWriter(file.getAbsoluteFile());
			BufferedWriter bw = new BufferedWriter(fw);
			bw.write(line);
			bw.close();
 			
			System.out.println(line);
			
			JsonParser.parseJson(video_code);
		}
4. Then, we parsed out the objects, title and duration from this JSON object (in the JsonParser class).

The code snippet for the same looks like : ::
                 JSONObject obj = new JSONObject(jsonStr);
		 String title = obj.getJSONObject("data").getString("title");
		 System.out.println(title);
		 JSONObject obj2 = new JSONObject(jsonStr);
		 int duration = (int) obj2.getJSONObject("data").get("duration");
		 System.out.println(duration);
		 Database.putdata(video_code,title,duration);

5. Finally this information was stored back in the table 'video_information' (in the Database class).

This process was repeated for all video codes.(Run the Download only when new entries are required)

Connection with hive was made by using the Connect class.

After the video_information table is ready, the main queries for extraction of seek time can be implemented.

:Extracting the amount of time a student seeked(or skipped) a video: 

This feature is concerned with extracting the amount of time a student has skipped a portion of the video. If a student is seeking a video more than the amount of his/her viewed time, then the student is likely not interested in the course (But it is also possible that a student is skipping one video only beacuse he/she has some knowledge about that topic. This is difficult to track beacuse we cannot estimate a student's knowledge on a specific topic. And it is very rare to find a student who will seek almost all the videos in a given course, provided that he/she already knows about this topic. In that case the student wouldn't have selected the course. As we are keeping track of the seek time of all videos in a course for each student, the case of a student seeking just one or two odd videos beacause he/she had some previous knowledge in it will be handled in the mapping function later described in futher topic).

The  java class written for this purpose is Feature_seek_time. The following snippet fulfills the above described functionality :::

   statement.executeQuery("drop table temp0");
   statement.executeQuery("create table temp0(code string,username string,seek int)");
   statement.executeQuery("create table seek_time_total(code string,username string,seek int,title string,duration int)");
   statement.executeQuery("insert into table temp0 select sv.code,sv.username,sum(sv.new_time-sv.old_time) from seek_video sv where log_id>="+start+" and log_id<"+end+" and not exists(select * from temp0 t2 where sv.code=t2.code and sv.username=t2.username) group by sv.code,sv.username ");
   resultset=statement.executeQuery("select * from temp0");
   statement.executeQuery("insert overwrite table seek_time_total select a.code,a.username,a.seek,b.title,b.duration from temp0 a join  video_information b on a.code=b.code");
   resultset=statement.executeQuery("select * from seek_time_total");
   while(resultset.next())
   {
   System.out.println(resultset.getString(1)+"\t"+resultset.getString(2)+"\t"+resultset.getString(3)+"\t"+resultset.getString(4)+"\t"+resultset.getString(5)+"\t");
   }
   statement.executeQuery("insert overwrite table status select name, instring, case when name='seek_time_processed' then "+end+" else inint end as inint from status");


The sample output space looks like :
  ===========   ======== ====    =============================   ========
  code		username seek    title                           duration
  ===========   ======== ====    =============================   ========
  RU2qJTO0Gms	ak	 764	 IntroductionToAndroidPart1      927	
  RU2qJTO0Gms	sachin   696	 IntroductionToAndroidPart1	 927	
  KdX4DaFRAKU	ak	 440	 Android UI and Layouts part-1	 415	
  KdX4DaFRAKU	oshin	 26	 Android UI and Layouts part-1	 415	
  2E_KTtnbzVU	sachin   269	 Android UI and Layouts part 3	 395	
  aI1uMZMmnY8	sachin   181	 Android UI and Layouts part 5	 351	
  d45uLZEU5U0	oshin	 758	 Introduction To Android Part2	 782	
  ===========   ======== ====    =============================   ========
To execute this query, we used an intermediate table named temp0. The table temp0 holds the 11 digit code of the video, username and seek time of a user whose entry for a particular video code is not present in the table. The seek time has been claculated by the difference in the new_time and old_time fields in the seek_video table. Only those entries are considered while query execution which have not been processed yet. This has been taken care by the status table entry 'seek_time_processed' which contains the log_id of the user who's entry has been processed last. This is yet another example of an incremental query which uses two variables 'start' and 'end' to implement this concept.

Then the table 'seek_time_total' finally contains the code, username, seek time(in seconds), title and duration of the video(in seconds). The video_information table gives the details about the title and duration of the video in seconds.


:Points to be noted:

1. The statements drop table temp0, creation of temp0 and seek_time should be executed for the first time only and then comment these lines once done.
2. Also, if a student has seen the video completely then a pause event is generated and no special event as to whether he/she has completed watching the video or not is not generated. Thus there will be a problem when a student has almost seen the video and also when the video will be watched multiple times.

Feature 3
~~~~~~~~~
| Measure of how much each user of each course is active per day. For this the entries of log tables have been taken.Two types of events have been considered :-
:play_video: 
| This activity gives an estimate of how much active an user is in case of watching video.Though this does not gives an exact measure of how active an user is in watching videos , but gives an approximate idea about how many times user plays and pauses the videos .
 
:problem_check:
This activity gives an estimate of how many problems a user has attempted.The final answer of this query has been stored in the table
activity_per_day.
| This table has the following columns:-
* username
* day
* course_id
* video_acts
* quiz_acts
| For implementing runtime queries , we make use of three intermediate tables namely :-
:video_activity:
* username
* day
* course_id
* count
* quiz_activity
* username
* day
* course_id
* count
  
| These two tables contains for a particular course,particular username,particular day how many videos were played and and how many questions attempted respectively.A third table which contains a join of these two tables on username,course_id and day contains actual count  of videos played and questions attempted by each user on each day of every courses.This table contains entries which needs to be put on the table activity_per_day.If the entries already exists , those are updated and those are not present,new entries are created for them.The structure of temp table is as follows :-
* temp
* username
* day
* course_id
* count_video
* count_quiz

| Since we need to create runtime queries , we should consider only those entries in the log tables which are new.So we keep a track of the log_id which have been processed in the status table.Let this be old_val.Maximum of log_id is taken to be as new_val.Only those entries are considered whose lod id are greaater than old_val and less than or equal to new_val.After that the status table is updated with the new_val as the entry for log_id already processed.

Feature 4
~~~~~~~~~~
Next feature deals with the difficulty level of each question.This difficulty level of a question is based on the information that how many students have attempted that question and in how many attempts.The final result of this query is stored in the following table:-
:diff_level:
* problem_id
* attempts
* no_of_users
* level
| Since we need to make the query runtime, we keep a note of log_id which has been tracked till now in the status table.We fetch that value and parse only those entries from the table problem_check_server whose log_id is greater than that value.The result of the query that which questions have been attempted by how many students and in how many attempts is stored in a temporary table :-

:record:
* problem_id
* username
* attempts


| From this table we can calculate how many students attempted a particular question and in total how many attempts.We can use that vaue to update the difficulty leve of the questions whose entries already exists in table difficulty_level. The entries which do not exists , for those dummy entries are created and then updated.following id the query written for the same.

| Let x be the number of attempts recored against a question and y be the number of users involved in that.So now the difficulty level of a question is updated as follows:-

| new level=(old_level*no_of_users+x)/(no_of_users +y)

| and the no_of_users column is updated as 
| no_of_users=no_of_users+y


