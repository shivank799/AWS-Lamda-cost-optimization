# AWS Lambda Function for EBS Snapshot Cleanup

## Overview
This AWS Lambda function is designed to automate the cleanup of unused or unnecessary EBS (Elastic Block Store) snapshots in your AWS account. It ensures that snapshots are only retained if they are attached to an active EC2 instance, helping optimize storage and reduce costs.

### Key Features:
- Deletes snapshots that are not associated with any EBS volume.
- Deletes snapshots from volumes that are no longer attached to running EC2 instances.
- Automatically handles cases where the associated volume has been deleted.
  
This function is useful for managing the lifecycle of EBS snapshots, especially in environments with frequent snapshot creation or large volumes of EC2 instances.

## Requirements

- **AWS Account**: Required to access EC2 and Lambda services.
- **IAM Role Permissions**: The Lambda execution role must have the following permissions:
  - `ec2:DescribeSnapshots` — To list EBS snapshots.
  - `ec2:DescribeInstances` — To list running EC2 instances.
  - `ec2:DescribeVolumes` — To check the state of volumes.
  - `ec2:DeleteSnapshot` — To delete unnecessary snapshots.

- **AWS Lambda**: Python 3.x runtime.

## Setup Instructions

### 1. **Create the Lambda Function**:
   - Log into the AWS Management Console.
   - Navigate to **AWS Lambda**.
   - Click **Create function** and select **Author from Scratch**.
   - Choose **Python 3.x** as the runtime.
   - Attach an IAM role with the necessary permissions or create a new one.

### 2. **Deploy the Function**:
   - Copy and paste the provided code into the Lambda function editor.
   - Save and deploy the function.

### 3. **Schedule the Lambda to Run Periodically**:
   - Go to **CloudWatch** in the AWS console.
   - Under **Rules**, create a new rule with a scheduled event (e.g., once a week).
   - Set the Lambda function as the target for the rule.

### 4. **Monitor the Lambda Logs**:
   - After deployment, check the logs in **CloudWatch Logs** to ensure the function is running as expected.

## Lambda Code Description

The Lambda function performs the following steps:

1. **Retrieve All Snapshots**: It uses the `describe_snapshots()` method to fetch all snapshots owned by the current AWS account.
  
2. **Get All Active EC2 Instances**: It retrieves all running EC2 instances with the `describe_instances()` method, filtering for instances in a "running" state.

3. **Check Snapshot Attachments**:
   - For each snapshot:
     - If the snapshot is not associated with a volume, it will be deleted.
     - If the snapshot is linked to a volume, the function checks whether the volume is attached to a running EC2 instance. If not, the snapshot is deleted.
     - If the associated volume is no longer found (e.g., deleted), the snapshot is deleted.

4. **Delete Unused Snapshots**: Snapshots that are no longer needed are deleted to free up space and reduce costs.

## Example Logs

After the Lambda function runs, the following types of log messages will appear in **CloudWatch Logs**:

