![Batch drawio](https://github.com/user-attachments/assets/53c2e8a5-e60e-48b6-8c4e-9b101a3cbda9)

# Serverless Architecture for CSV Batch Processing

This project implements an AWS serverless architecture for automatic processing of CSV files using AWS Batch, EventBridge, Lambda, and Docker containers.

## Overview

The architecture is implemented through CloudFormation and provides:

- Automatic processing of CSV files when uploaded to an S3 bucket
- Scalable batch processing through AWS Batch with Fargate Spot
- Storage of results in a separate S3 bucket
- Monitoring through CloudWatch

## Main Components

- **S3**: Two buckets (input and output)
- **Lambda**: Trigger function that starts batch jobs when a CSV file is uploaded
- **AWS Batch**: Fargate compute environment and job queue
- **ECR**: Repository for the custom Docker image
- **VPC**: Private network with public and private subnets
- **CloudWatch**: Logging and alarms

## Processing Flow

1. A CSV file is uploaded to the source S3 bucket
2. EventBridge detects the upload and triggers the Lambda function
3. Lambda initiates an AWS Batch job
4. AWS Batch runs the Docker container that:
   - Downloads the CSV file from the source bucket
   - Processes the CSV (adds a processing timestamp)
   - Uploads the processed file to the destination bucket

## Deployment

To deploy this architecture, run:

```bash
aws cloudformation create-stack \
  --stack-name csv-batch-processor \
  --template-body file://BatchCSV.yaml \
  --parameters file://parameters.json \
  --capabilities CAPABILITY_IAM CAPABILITY_NAMED_IAM
```

## Monitoring and Logging

- Lambda function logs are available in CloudWatch Logs at `/aws/lambda/{StackName}-trigger-batch-job`
- Batch job logs are available in CloudWatch Logs at `/aws/batch/{StackName}`
- CloudWatch alarms alert in case of errors in batch jobs or the Lambda function

## Security

The architecture includes:
- Secure networking with private subnets for Batch jobs
- S3 bucket encryption
- IAM roles with minimal privileges
- Blocking public access to S3 buckets
