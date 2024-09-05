# AWS Cloud Cost Optimization - Identifying Stale Resources

## Identifying Stale EBS Snapshots

In this guide, we detail a method to optimize AWS Cloud storage costs by identifying and deleting EBS snapshots that are no longer associated with any active EC2 instance.

### Description

The process involves creating an AWS Lambda function designed to perform the following tasks:

1. **Fetch EBS Snapshots**: The Lambda function retrieves all EBS snapshots owned by the account, specifically targeting snapshots owned by 'self'.
2. **Retrieve Active EC2 Instances**: It then fetches a list of active EC2 instances, including both running and stopped instances, to determine the current utilization of resources.
3. **Identify Stale Snapshots**: For each EBS snapshot, the function checks if the associated volume is linked to any active EC2 instance. The absence of an association indicates that the snapshot is stale.
4. **Delete Stale Snapshots**: Upon identifying a stale snapshot, the Lambda function proceeds to delete it. This action removes the unused snapshot, thereby optimizing storage costs by eliminating unnecessary expenditure on storage space.

This Lambda function plays a critical role in managing AWS Cloud costs efficiently by automating the identification and removal of stale resources that no longer contribute to the operational needs of the account.


### Deploying on AWS Lambda

To deploy the Lambda function for identifying and deleting stale EBS snapshots, follow these steps:

#### 1. Create the Lambda Function
- Navigate to the AWS Lambda console.
- Choose `Create function` and select `Author from scratch`.
- Enter a function name and select a runtime (e.g., Python 3.x ).
- Click on `Create function`.


![Lambda Image](https://github.com/werkmanne/lambda-cost-optimization-project/blob/main/Screenshot%202024-03-06%20155137.png "AWS Lambda Image")

![Lambda Image](https://github.com/werkmanne/lambda-cost-optimization-project/blob/main/Screenshot%202024-03-06%20155520.png "AWS Lambda Image")

![Lambda Image](https://github.com/werkmanne/lambda-cost-optimization-project/blob/main/Screenshot%202024-03-06%20155724.png "AWS Lambda Image")



#### 2. Increase Execution Timeout
- By default, AWS Lambda sets the function timeout to 3 seconds. For this task, increase the timeout to up to 10 seconds to ensure the function has sufficient time to process snapshots.
- In the `Basic settings` panel, click on `Edit`.
- Set the `Timeout` to 10 seconds and save the changes.

![Lambda Image](https://github.com/werkmanne/lambda-cost-optimization-project/blob/main/Screenshot%202024-03-06%20155939.png "AWS Lambda Image")


### Configuring IAM Permissions

The Lambda function requires permissions to describe EC2 instances and manage EBS snapshots. Follow these steps to create an IAM role with the necessary permissions:

#### 1. Create an IAM Role
- Go to the IAM console and select `Roles` > `Create role`.
- Choose AWS service as the trusted entity and select `Lambda` as the use case.
- Click `Next: Permissions`.

#### 2. Attach Policies
- Attach the `AmazonEC2ReadOnlyAccess` policy to allow the function to describe EC2 instances.
- Attach a custom policy for managing EBS snapshots. You can create a custom policy with the following permissions:
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
      {
        "Effect": "Allow",
        "Action": [
          "ec2:DescribeSnapshots",
          "ec2:DeleteSnapshot"
        ],
        "Resource": "*"
      }
    ]
  }


![Lambda Image](https://github.com/werkmanne/lambda-cost-optimization-project/blob/main/Screenshot%202024-03-06%20160449.png "AWS Lambda Image")

![Lambda Image](https://github.com/werkmanne/lambda-cost-optimization-project/blob/main/Screenshot%202024-03-06%20160944.png "AWS Lambda Image")


