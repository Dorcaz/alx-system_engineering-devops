	Incidence Report
-Issue Summary
Duration with time zone: From 9:15AM to 10:00AM WAT
Outage Impact: Requests to the homepage of our wordpress server returns 500 error respose messages. At its peak the issue affected 100% of traffic to this webserver. 
Root Cause: The root cause of this outage was an error in the file extension of .phpp instead of .php

Timeline
	9:10 AM : Updates push
    • 9:15 AM : Noticing the problem
    • 9:15 AM : Notifying the both front end and backend teams
    • 9:20 AM : Successful change rollback
    • 9:24 AM : Server Restarts begin
    • 9:27 AM : 100% of traffic back online
    • 9:30 AM : start debugging the push with the problem
    • 9:50 AM : Probelm fixed and pushed the changes
    • 9:55 AM : Server restart begins
    • 10:00 AM : 100% traffic back online with the new updates


Root Cause:
At 9:10AM WAT, an update was pushed to the production environment without first being released to the testing environment. The change specified an invalid file extension for the authentication server in production. This exposed a bug in the uthentication libraries which caused them to block permanently while attempting to resolve the invalid address to physical services. In addition, the internal monitoring systems permanently blocked on this call to the authentication library.. The server became unreachable.

Resolution and Recovery
At 9:15 AM WAT, the monitoring systems alerted our engineers who investigated and quickly escalated the issue. 
At 9:20AM, we started a rollback to fix the issue. After rolling back changes we knew that the changes were made by the front end team so we took the broken changes and run them on a test server which replicated same problem, our server uses apache2 and apache2 error logs didn't give enought infomation about the problem so we traced the apache2 process using strace and when a request is sent strace tool catchs a lot of error and after some scaning fo these errors we found the error wich is a typo in page file extention >

.phpp

instead of

.php

and to fix that we just search in our main directory using grep for that typo

grep -inR ".phpp" .

after fixing the error we pushed back the changes and restarted the servers

10:00 AM : 100% of trafic back online with the new updates

Corrective and Preventive
To prevent similar problems from happening again we will
    • Create an automated test pipeline for every update push
    • Add a monitoring software to our servers which will monitor lot of things and one of them Network Traffic resquests and responses and configure it to make an lert to the teams when too much non desired responses were sent like 500
    • Create a tests for every new update and the teams shouuld not push until those tests pass

