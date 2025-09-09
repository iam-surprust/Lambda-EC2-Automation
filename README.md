1. Create an IAM Roles for AWS Lambda
Create and IAM role for AWS Lambda to get permission to access Cloud Watch Logs and EC2 so it can invoke the Lambda Function.
The JSON Document for IAM Role name- Lambda_stop_start_instances_Role

2. IAM Role for Event Bridge
The IAM role for Event Bridge is created by default while creating an Event. Hence, we can use the same IAM role created by Default.
 
Create Event Bridge Schedules
Create a Schedule to Stop the Instances
Schedule name- Stop_EC2_Instances
Schedule pattern
Occurrence - Recurring schedule
Time zone- Select based on Client TimeZone
Schedule type- Cron-based schedule
Cron expression – E.g. - cron(30 17 ? * MON,TUE,WED,THU,FRI *)
Note- Cron expression Explanation-
30: The task will run at the 30th minute of the hour.
17: It will run at the 17th hour of the day (which is 5 PM).
?: The question mark in the day-of-month field denotes no specific value and is used as a placeholder because the day of the week is specified.
*: The asterisk in the month field indicates every month.
MON,TUE,WED,THU,FRI: The task will run on weekdays only, from Monday to Friday.
So, this cron expression means that the scheduled task will run at 5:30 PM every weekday.
 
Target detail - Target API – AWS Lambda
Invoke - AWS Lambda
Lambda function- Stop_EC2_Instances
Schedule state - Enable
Dead-letter queue (DLQ)- None
Select the Execution role- Amazon_EventBridge_Scheduler_LAMBDA
Note- The execution role becomes automatically available once we select the Target details.
Review and Create the Event Bridge Schedule
Similarly Create a Schedule to start the Instances
Schedule Name- Start_EC2_Instances
