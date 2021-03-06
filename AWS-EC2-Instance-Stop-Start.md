# EC2InstanceStart-Stop
This is a template to start and stop the ec2 instance using lambda function in AWS Cloud Watch
### How do I stop and start Amazon EC2 instances at regular intervals using Lambda?

I want to reduce my Amazon Elastic Compute Cloud (Amazon EC2) usage by stopping and starting my EC2 instances automatically. How do I use AWS Lambda and Amazon CloudWatch Events to do that?

For this setup, do the following:

*    Create a custom AWS Identity and Access Management (IAM) policy and execution role for your Lambda function.

*   Create Lambda functions that stop and start your EC2 instances.

*   Create CloudWatch Events rules that trigger your function on a schedule. For example, you could create a   rule to stop your EC2 instances at night, and another to start them again in the morning.

## Practical Approach to achieve the above task

## Create an IAM policy and role

1.    Create an IAM policy using the JSON policy editor. Paste this JSON policy document into the policy editor:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ec2:Start*",
        "ec2:Stop*"
      ],
      "Resource": "*"
    }
  ]
}
```

2.    Create an IAM role for Lambda. When attaching a permissions policy, search for and choose the IAM policy that you created.

## Create Lambda functions that stop and start your EC2 instances

1.    In the Lambda console, choose Create function.
2.    Choose Author from scratch.
3.    Under Basic information, add the following:
For Function name, enter a name that identifies it as the function used to stop your EC2 instances. For example, "StopEC2Instances".
For Runtime, choose Python 3.7.
Under ``` Permissions ```, expand Choose or create an execution role.
Under ```Execution role ```, choose Use an ``` existing role ```.
Under ``` Existing role ```, choose the IAM role that you created.

4.    Choose ``` Create function ```.

5.    Copy this code, and then under ``` Function code ```, paste it into the editor pane in the code editor (lambda_function). This code stops the EC2 instances that you identify.

Note: For region, replace "us-west-1" with the AWS Region that your instances are in. For instances, replace the example EC2 instance IDs with the IDs of the specific instances that you want to stop and start.

```
import boto3
region = 'us-west-2'
instances = ['i-008c5138f7ea92055', 'i-0c3f961f336c9f9d6']
ec2 = boto3.client('ec2', region_name=region)

def lambda_handler(event, context):
    ec2.stop_instances(InstanceIds=instances)
    print('stopped your instances: ' + str(instances))

```
6.    Under Basic settings, set ``` Timeout ``` to 10 seconds.
Note: Configure the Lambda function settings as needed for your use case. For example, if you want to stop and start multiple instances, you might need a different value for Timeout, as well as Memory.

7.    Choose Save.

8.    Repeat steps 1-7 to create another function. Do the following differently so that this function starts your EC2 instances:
In step 3, enter a Function name it as the function used to start your EC2 instances. For example, "StartEC2Instances".
In step 5, copy and paste this code into the editor pane in the code editor (lambda_function):

### For starting the ec2 instance lambda code is provided below:

```
import boto3
region = 'us-west-2'
instances = ['i-008c5138f7ea92055', 'i-0c3f961f336c9f9d6']
ec2 = boto3.client('ec2', region_name=region)

def lambda_handler(event, context):
    ec2.start_instances(InstanceIds=instances)
    print('started your instances: ' + str(instances))`

```
## Cron time at every day at 10 PM
``` 0 22 * * ? * ```
## Cron time at every day at 6 AM
```0 6 * * ? * ```
## Cron time at every day at 7:00 PM
``` 0 19 * * ? *   ```
## Cron time at every day at 10:00 AM
``` 0 10 * * ? * ```

## Test your Lambda functions

1.    In the Lambda console, choose Functions.

2.    Select one of the functions that you created.

3.    Choose Actions, and then choose Test.

4.    In the Configure test event dialog, choose Create new test event.

5.    Enter an Event name, and then choose Create.

Note: You don't need to change the JSON code for the test event—the function doesn't use it.

6.    Choose Test to execute the function.

7.    Repeat steps 1-6 for the other function that you created.

Tip: You can check the status of your EC2 instances before and after testing to confirm that your functions work as expected.

### Create rules that trigger your Lambda functions

1.    Open the CloudWatch console.

2.    In the left navigation pane, under Events, choose Rules.

3.    Choose Create rule.

4.    Under Event Source, choose Schedule.

5.    Do either of the following:
For Fixed rate of, enter an interval of time in minutes, hours, or days.
For Cron expression, enter an expression that tells Lambda when to stop your instances. For information on the syntax of expressions, see Schedule Expressions for Rules.
Note: Cron expressions are evaluated in UTC. Be sure to adjust the expression for your preferred time zone.

6.    Under Targets, choose Add target.

7.    Choose Lambda function.

8.    For Function, choose the function that stops your EC2 instances.

9.    Choose Configure details.

10.    Under Rule definition, do the following:
For Name, enter a name to identify the rule, such as "StopEC2Instances".
(Optional) For Description, describe your rule. For example, "Stops EC2 instances every night at 10 PM."
For State, select the Enabled check box.

11.    Choose Create rule.

12.    Repeat steps 1-11 to create a rule to start your EC2 instances. Do the following differently:
In step 5, for Cron expression, enter an expression that tells Lambda when to start your instances.
In step 8, for Function, choose the function that starts your EC2 instances.
In step 10, under Rule definition, enter a Name like "StartEC2Instances", and optionally enter a Description like "Starts EC2 instances every morning at 6 AM."










