=================
**INTRODUCTION**
=================

Data analytics is the discovery and communication of meaningful patterns in data. There is extensive use of mathematics and statistics, the use of descriptive techniques and predictive models to gain valuable knowledge from data - data analysis. The insights from data are used to recommend action or to guide decision making rooted in business context. Thus, analytics is not so much concerned with individual analyses or analysis steps, but with the entire methodology. From our project's point of view, we are concerned with 'EdX Analytics'. In this, we have to figure out the different patterns hidden in the log files generated.

The log files have entries for every activity ever made by a student on the edX platform. Each and every click on tabs or question attempted or a video being played, all the entries are created in the log file. From this log file, we parse out the relevant information and find out patterns to detect that a student is showing any actions involving gaming the system. Gaming, as described above, is any behaviour aimed at obtaining correct answers and advancing within the tutoring curriculum by systematically taking advantage of regularities in the software’s feedback and help. Thus, classifying the gaming students is our priority. This involves careful feature extraction, data analytics and machine learning on the features for classification.

Feedback to the ITS designer and course tutor is equally important. It is essential to know whether the system is doing a good work or not. This involves calculating a student's response based on various categories. A visualized form of such information is useful to them for deciding what reformations they need to apply, so as to make the course even more compelling and interesting to the students.

============
**PURPOSE**
============

The main purpose of our project is to determine which student is gaming the system so that we can apply interventions in their tutor system. For example if a question has some hint options, then a student can repeatedly use that hint option to reach to the correct answer. But, this kind of behaviour tampers the process of learning of the student. So, our main aim is to filter out such students who get involved in off-task behaviours and implement interventions only on those students' tutor system, which would passify their gaming process and indirectly motivate them to learn and attempt the quizes whole-heartedly. To implement such a system, relevant feature extraction from the log files is a necessity. Then comes the use of machine learning to classify a student as gaming or not gaming based on the features extracted.

Secondly, data analytics also involves a thorough study of the database containing all the demographic and activity information of a user. From this data, one can infer as to which category of students are mostly interested in learning the course. This requires queries to be written on that data from which we can extract relevant information. A visualized form of such information needs to be created as a feedback to the course designers. Looking at this, they can decide what reformations they need to apply, so as to make the course seem even more compelling and interesting to the students.


==========
**SCOPE**
==========

Implementing the ideas described above will make the tutoring system very efficient while grading a student. Any normal ITS, without any provisions for detection of a student who is gaming and implementing interventions in their system, will award a certificate to any student who has completed the course. But this would make a system very incompetent in correctly grading an undeserving student. Thus our idealogies would aid the ITS in fairly classifying the students into gaming and not gaming and accrordingly award the certificates to the deserving students. In addition, the tutoring system will be able to pick out the 'gaming' students and interevene their learning process so that their learning skills also match the regular students' learning, thereby widening the scope of the efficiency of the edX course site.

