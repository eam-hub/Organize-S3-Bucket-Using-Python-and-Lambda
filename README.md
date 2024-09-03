# Organize S3 Bucket Using Python and Lambda

This project demonstrates how to organize files in an S3 bucket by creating a Lambda function that organizes files into folders named after the date they were uploaded. This guide provides an introduction to using AWS Lambda with Python, showcasing how to automate file organization in S3 buckets.

## Prerequisites

- **Python**: Install Python on your system.
- **Boto3**: Install Boto3, the Python SDK for AWS, to interact with AWS services.
- **IAM User**: Create an IAM user with access keys for authentication.
- **AWS CLI**: Install the AWS CLI to manage and interact with AWS services.
- **Terraform**: Install Terraform for infrastructure management.
- **Visual Studio Code**: Install VSCode with the Pylance and Jupyter extensions for Python development.
- **Git**: Install Git to clone repositories.
- **SSH Key**: Create SSH key pairs to clone private GitHub repositories.

## Setup Instructions

1. **Create an S3 Bucket**
   - Create an S3 bucket where you will upload files.

2. **Clone the GitHub Repository**
   - Copy your GitHub repository SSH key.
   - In your command prompt, navigate to the directory where you want to clone your repository:
     ```bash
     git clone git@github.com:eam-hub/organize-S3-Python-Project.git
     ```
   - Replace `git@github.com:eam-hub/organize-S3-Python-Project.git` with your repository's SSH URL.

3. **Write the Python Code**
   - Open Visual Studio Code.
   - Create a new file with a `.ipynb` extension (e.g., `Organize_S3_Objects.ipynb`) and write your Python code using Jupyter Notebook.
   - Save the notebook as a Python file (`.py`) once the code is complete.

   ```python
   import boto3
   from datetime import datetime

   today = datetime.today()
   todays_date = today.strftime("%Y%m%d")

   def lambda_handler(event, context):
       S3_client = boto3.client('s3')
       bucket_name = "eam-organize-s3-bucket-project"
       list_objects_response = S3_client.list_objects_v2(Bucket=bucket_name)
       get_contents = list_objects_response.get("Contents")

       get_all_s3_objects_and_folder_names = []
       for item in get_contents:
           s3_object_name = item.get("Key")
           get_all_s3_objects_and_folder_names.append(s3_object_name)

       directory_name = todays_date + "/"
       if directory_name not in get_all_s3_objects_and_folder_names:
           S3_client.put_object(Bucket=bucket_name, Key=(directory_name))

       for item in get_contents:
           object_creation_date = item.get("LastModified").strftime("%Y%m%d") + "/"
           object_name = item.get("Key")

           if object_creation_date == directory_name and "/" not in object_name:
               S3_client.copy_object(Bucket=bucket_name, CopySource=bucket_name+"/"+object_name, Key=directory_name+object_name)
               S3_client.delete_object(Bucket=bucket_name, Key=object_name)
   ```

4. **Prepare and Upload the Python Code**
   - Navigate to the directory where your Python code file is located.
   - Right-click the file and compress it into a ZIP folder.
   - Upload the ZIP file to an S3 bucket.

5. **Create IAM Role for Lambda Function**
   - Create an IAM role with the following policies:
     - `AmazonS3FullAccess`
     - `AWSLambdaBasicExecutionRole`

6. **Create Lambda Function**
   - Go to the AWS Lambda console.
   - Click "Create function" and choose "Author from scratch."
   - Select the latest Python runtime.
   - Assign the IAM role created in the previous step.
   - Set the S3 bucket as the trigger:
     - Choose the S3 bucket and set the event type to "PUT."
   - Upload the ZIP file containing your Python code to the Lambda function.

7. **Update Lambda Handler Settings**
   - Set the handler name to match the Python script and function (e.g., `organize_s3_objects.lambda_handler`).

8. **Test the Lambda Function**
   - Upload a new file to your S3 bucket.
   - Check the S3 bucket to ensure that a new folder with the date is created and the file is moved accordingly.

---

This README file provides a step-by-step guide to setting up and using a Lambda function to organize files in an S3 bucket. Ensure that all necessary permissions and configurations are correctly set to avoid issues during execution.
