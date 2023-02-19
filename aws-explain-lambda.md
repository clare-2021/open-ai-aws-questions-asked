# Question
aws services how does lambda work

# Answer
AWS Lambda is a serverless computing service provided by Amazon Web Services (AWS). 
It allows you to run your code without provisioning or managing servers. Instead, AWS Lambda executes
your code only when needed and automatically scales to handle any request volume. This makes it an ideal
solution for running code in response to events, such as HTTP requests, changes to data in a database, 
or messages in a queue.

Here's a high-level overview of how AWS Lambda works:

- You write your code in a supported programming language (such as Node.js, Python, Java, or C#), 
and package it as a ZIP file.
- You create a Lambda function in the AWS Management Console or using the AWS CLI. You specify the name
 of the function, the runtime environment, and any other configuration settings, such as the amount
 of memory and CPU to allocate to the function.
- You upload your code package to the Lambda function.
- You configure a trigger for the Lambda function. This could be an event from an AWS service, such as 
an S3 bucket, a Kinesis stream, or an API Gateway endpoint, or it could be a custom event, such as a message
 in an Amazon SNS topic or an Amazon SQS queue.
- When an event occurs that triggers the Lambda function, AWS Lambda automatically provisions the necessary
 compute resources and runs your code. The function can access other AWS services and resources, such as
 DynamoDB or S3, via AWS SDKs or other AWS service integrations.
- When the function completes execution, AWS Lambda automatically releases the compute resources and logs
 the execution details.
- One of the main benefits of AWS Lambda is that you only pay for the compute time that your
 code actually uses. This means that you can run your code without worrying about idle server time or
 over-provisioning your infrastructure.

# Question
show me an example of an aws lambda function in python for ssh into another server and copying a file in
json fomat from it to an s3 bucket on a daily basis

# Answer
Here is an example of an AWS Lambda function written in Python that connects to a remote server via SSH and 
copies a file in JSON format to an S3 bucket on a daily basis:
```
import boto3
import paramiko
from datetime import datetime, timedelta

def lambda_handler(event, context):
    # Set up S3 client
    s3 = boto3.client('s3')
    bucket_name = 'your-bucket-name'
    s3_file_key = 'path/to/your/file.json'

    # Set up SSH client
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect('your-server-ip', username='your-ssh-username', password='your-ssh-password')

    # Get current date in yyyy-mm-dd format
    current_date = datetime.now().strftime('%Y-%m-%d')

    # Set up remote file path and local file path
    remote_file_path = f'/path/to/your/remote/file_{current_date}.json'
    local_file_path = f'/tmp/file_{current_date}.json'

    # Copy file from remote server to local machine
    sftp = ssh.open_sftp()
    sftp.get(remote_file_path, local_file_path)
    sftp.close()

    # Upload file to S3 bucket
    with open(local_file_path, 'rb') as f:
        s3.upload_fileobj(f, bucket_name, s3_file_key)

    # Clean up local file
    os.remove(local_file_path)
```
- This function uses the boto3 library to interact with S3, and the paramiko library to connect to the
 remote server via SSH. It first sets up the S3 client and specifies the bucket name and S3 file key to use
 for the file upload. It then sets up the SSH client and connects to the remote server using the specified
 SSH username and password.

- The function gets the current date in yyyy-mm-dd format and constructs the remote file path and 
local file path accordingly. It then uses the sftp object to copy the remote file to the local machine, and
 closes the SFTP connection. The function then uploads the local file to the specified S3 bucket using
 the upload_fileobj method.

- Finally, the function cleans up the local file by removing it from the /tmp directory. You can schedule
 this function to run on a daily basis using a CloudWatch Event rule.



