Automating AWS EC2 Start/Stop with Lambda and EventBridge Scheduler

Managing AWS costs is a top priority for most organizations, and one simple yet effective optimization is to automatically stop non-production EC2 instances during non-working hours and start them back when needed. Recently, I implemented this setup using AWS Lambda, EventBridge Scheduler, and boto3, and I’d like to share how this works in a detailed but easy-to-follow way.

<img width="805" height="805" alt="image" src="https://github.com/user-attachments/assets/8206d2e5-2db1-412e-a0bf-c335439fd1b0" />


Why Automate EC2 Start/Stop?

•⁠  ⁠Cost Savings: EC2 instances incur charges when running, so shutting them down outside of business hours can significantly reduce monthly costs.
•⁠  ⁠Efficiency: Manual stopping and starting is error-prone. Automation ensures consistency.
•⁠  ⁠Scalability: Works across multiple accounts, regions, or instance groups without manual intervention.

 Components Used

1.⁠ ⁠AWS Lambda – Serverless compute that runs Python code to start/stop EC2 instances.
2.⁠ ⁠boto3 – AWS SDK for Python, used inside Lambda to interact with EC2 APIs.
3.⁠ ⁠EventBridge Scheduler – Defines cron-like rules to trigger Lambda at specific times (e.g., stop at 8 PM, start at 8 AM).

High-Level Architecture

1.⁠ ⁠EventBridge Scheduler triggers Lambda based on time rules.
2.⁠ ⁠Lambda Function executes boto3 commands to stop or start specific EC2 instances.
3.⁠ ⁠CloudWatch Logs capture execution results for monitoring and troubleshooting.

 Implementation Steps

1.⁠ ⁠Create IAM Role for Lambda

•⁠  ⁠Permissions required: ⁠ ec2:StartInstances ⁠, ⁠ ec2:StopInstances ⁠, ⁠ ec2:DescribeInstances ⁠.
•⁠  ⁠Attach a policy like ⁠ AmazonEC2FullAccess ⁠ or a custom least-privilege policy.

2.⁠ ⁠Write the Lambda Function (Python + boto3)

•⁠  ⁠The function reads instance IDs from environment variables.
•⁠  ⁠EventBridge passes the action (start/stop) into the Lambda event payload.

3.⁠ ⁠Configure Environment Variables


4.⁠ ⁠Setup EventBridge Scheduler

•⁠  ⁠Rule 1: Trigger Lambda with ⁠ { "action": "stop" } ⁠ at 8 PM (local time).
•⁠  ⁠Rule 2: Trigger Lambda with ⁠ { "action": "start" } ⁠ at 8 AM (local time).
•⁠  ⁠Ensure correct time zone in the cron expressions.

Key Considerations

•⁠  ⁠Tag-Based Filtering: Instead of hardcoding instance IDs, you can modify the Lambda to filter by tags (e.g., ⁠ Environment=Dev ⁠).
•⁠  ⁠Error Handling: Add try/except blocks to handle API errors gracefully.
•⁠  ⁠Notifications: Integrate with SNS to notify teams when instances fail to start/stop.

Business Impact

•⁠  ⁠Immediate cost savings by reducing running hours for non-critical EC2 workloads.
•⁠  ⁠Improved governance by enforcing a consistent start/stop schedule.
•⁠  ⁠Flexibility to adapt schedules to evolving business and project needs.

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
