# Serverless NBA Game Notification System

This project is a serverless solution that uses AWS Lambda, Amazon SNS, and Amazon EventBridge to deliver real-time NBA game updates directly to your email.

## Description

NBA game data is pulled from SportsData.io using their public API. AWS Lambda fetches and formats the data, which is then published to an Amazon SNS topic. EventBridge triggers the Lambda function on a defined schedule, ensuring users receive up-to-date notifications.

## AWS Services Used

- AWS Lambda  
- Amazon SNS  
- Amazon EventBridge  
- IAM (Roles and Policies)  

## Technologies

- Python 3.13  
- SportsData.io API  
- AWS CloudWatch  

## Project Structure

serverless-nba-updates/
├── policies/
│   └── gd_sns_policy.json       # IAM policy for SNS publish  
├── src/
│   └── gd_notifications.py      # Lambda function code  
├── .gitignore  
└── README.md  

## Prerequisites

- AWS account  
- SportsData.io account with API Key  
- Basic knowledge of AWS Lambda and IAM  

## Step-by-Step Deployment

### 1. Clone the repository

git clone https://github.com/NazBilgic/serverless-nba-updates.git  
cd serverless-nba-updates

### 2. Create an SNS topic and subscribe

- Go to Amazon SNS → Create topic  
- Choose topic type: Standard  
- Add an Email subscription  
- Confirm via your email  

### 3. Create IAM policy

Go to IAM → Policies → Create Policy  
Paste the JSON below and replace REGION, ACCOUNT_ID, and TOPIC_NAME:

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

Save as gd_sns_policy.

### 4. Create IAM role for Lambda

- IAM → Roles → Create Role  
- Trusted entity: Lambda  
- Attach:
  - gd_sns_policy
  - AWSLambdaBasicExecutionRole (AWS managed)

### 5. Create the Lambda function

- AWS Lambda → Create Function  
- Runtime: Python 3.13  
- Assign IAM role created earlier  
- Paste code from `gd_notifications.py`  
- Set environment variables:
  - NBA_API_KEY  
  - SNS_TOPIC_ARN  

### 6. Test the Lambda function

- Create a test event  
- Run the function  
- Check email for NBA updates  
- Review logs in CloudWatch  

### 7. Schedule with EventBridge

- EventBridge → Rules → Create Rule  
- Event Source: Schedule (e.g., hourly)  
- Target: Your Lambda function  

## What You Learn

- Serverless architecture with Lambda  
- Secure IAM role and policy creation  
- API integration with external data  
- Automating workflows using EventBridge  

## Author

Naz Bilgic 
linkedin.com/in/nazbilgic
