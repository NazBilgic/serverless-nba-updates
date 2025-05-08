# Automating NBA Game Notifications with AWS Lambda, SNS, and EventBridge

In this project, we create a serverless solution using AWS services like AWS Lambda, SNS, and EventBridge to send real-time NBA game updates to your email.

---

## Prerequisites

- Free account with subscription and API Key at [sportsdata.io](https://sportsdata.io)
- Personal AWS account with basic understanding of AWS and Python

---

## Key AWS Components Used

- **AWS Lambda**: Executes code to fetch NBA game data.  
- **Amazon SNS**: Sends game updates to your email.  
- **Amazon EventBridge**: Triggers Lambda based on a schedule.  
- **IAM Policies**: Provides the necessary permissions to secure communication between services.

---

## Project Structure

game-day-notifications/
├── policies/
│ └── gd_sns_policy.json # SNS publishing permissions
├── src/
│ └── gd_notifications.py # Lambda function code
├── .gitignore
└── README.md # Project documentation

yaml
Copy
Edit

---

## Step-by-Step Implementation

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/serverless-nba-updates.git
cd serverless-nba-updates
2. Create an SNS Topic and Subscription
Navigate to Amazon SNS in the AWS Console.

Create a topic (Standard).

Add a subscription (Email or SMS).

Confirm the subscription via your email.

3. Set Up an IAM Policy
Create a policy to allow SNS topic publishing:

Go to IAM → Policies → Create Policy

Select JSON and paste the following:

json
Copy
Edit
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sns:Publish",
      "Resource": "arn:aws:sns:REGION:ACCOUNT_ID:TOPIC_NAME"
    }
  ]
}
Replace REGION, ACCOUNT_ID, and TOPIC_NAME with your values.

Name it (e.g., gd_sns_policy), then create the policy.

4. Create an IAM Role for Lambda
Go to IAM → Roles → Create Role

Choose Lambda as the trusted entity

Attach the following:

Your SNS policy (e.g., gd_sns_policy)

AWS managed policy: AWSLambdaBasicExecutionRole

Name it (e.g., gd_role) and save the ARN.

5. Create the Lambda Function
Go to AWS Lambda → Create function

Author from scratch

Runtime: Python 3.13

Assign IAM role (gd_role)

Paste the code into the inline editor:

python
Copy
Edit
import os
import json
import urllib.request
import boto3
from datetime import datetime, timedelta, timezone

def format_game_data(game):
    # Format game details based on status (Final, Scheduled, InProgress)
    pass

def lambda_handler(event, context):
    api_key = os.getenv("NBA_API_KEY")
    sns_topic_arn = os.getenv("SNS_TOPIC_ARN")
    sns_client = boto3.client("sns")
    
    today_date = (datetime.now(timezone.utc) - timedelta(hours=6)).strftime("%Y-%m-%d")
    api_url = f"https://api.sportsdata.io/v3/nba/scores/json/GamesByDate/{today_date}?key={api_key}"
    
    try:
        with urllib.request.urlopen(api_url) as response:
            data = json.loads(response.read().decode())
    except Exception as e:
        print(f"Error: {e}")
        return {"statusCode": 500, "body": "API Error"}
    
    messages = [format_game_data(game) for game in data]
    message = "\n---\n".join(messages) if messages else "No games today."
    
    try:
        sns_client.publish(TopicArn=sns_topic_arn, Message=message, Subject="NBA Game Updates")
    except Exception as e:
        print(f"SNS Error: {e}")
        return {"statusCode": 500, "body": "SNS Error"}
    
    return {"statusCode": 200, "body": "Success"}
Add environment variables: NBA_API_KEY and SNS_TOPIC_ARN

6. Test the Lambda Function
Create a test event and run the function.

Check your email for NBA game updates.

Review CloudWatch logs for any errors.

7. Schedule Lambda with EventBridge
Go to EventBridge → Rules → Create Rule

Event Source: Schedule

Define a rate (e.g., every 5 minutes or hourly)

Select the Lambda function as the target and create the rule.

✅ Test the System
Run the Lambda manually or wait for EventBridge to trigger it

Check CloudWatch Logs

Confirm that emails are sent via SNS

Conclusion
This project demonstrates how to build a serverless NBA game notification system using:

AWS Lambda

Amazon SNS

Amazon EventBridge

What You Learned
Designing real-time notification systems using AWS

Creating and attaching IAM policies with least privilege

Using external APIs inside serverless workflows

Automating tasks with EventBridge rules

Made with ❤️ by NazBilgic

