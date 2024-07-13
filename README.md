# Jenkins Pipeline for AWS EC2 Instance Management

This repository contains a Jenkins pipeline script to securely manage AWS EC2 instances using Jenkins and the AWS CLI.

## Prerequisites

Before setting up the pipeline, ensure you have the following:

- Jenkins server installed and configured.
- AWS IAM credentials (Access Key ID and Secret Access Key) with permissions to manage EC2 instances.
- Instances IDs listed in `instances.txt` file.

## Setup Instructions

### 1. Configure Jenkins Credentials

1. Log in to Jenkins and navigate to "Credentials" > "System" > "Global credentials (unrestricted)" > "Add Credentials".
2. Add the following credentials:
   - **AWS Access Key and Secret Access Key:** 
     - Kind: Username with password
     - Username: Your AWS Access Key ID
     - Password: Your AWS Secret Access Key
     - ID: `aws-access-key-id` (or your preferred ID)
   - **AWS Region:**
     - Kind: Secret text
     - Secret: Your AWS Region (e.g., `us-east-1`)
     - ID: `aws-region` (or your preferred ID)

### 2. Create Jenkins Pipeline Job

1. Click on "New Item" in Jenkins dashboard.
2. Enter a name for your job (e.g., `Stop EC2 Instances`).
3. Select "Pipeline" as the job type and click "OK".
4. Scroll down to "Pipeline" section and paste the following script:

```groovy
pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID = credentials('aws-access-key-id')  // Jenkins credential ID for AWS access key ID
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')  // Jenkins credential ID for AWS secret access key
        AWS_REGION = env.AWS_REGION  // Jenkins environment variable for AWS region
    }

    stages {
        stage('Run Jenkins Job') {
            steps {
                echo 'Running Jenkins job...'
                // Add your Jenkins job steps here
                // For example, running a build or test
            }
        }

        stage('Stop AWS EC2 Instances') {
            steps {
                script {
                    echo 'Stopping AWS EC2 instances...'
                    def instanceIds = readFile('instances.txt').trim()
                    sh """
                    export AWS_ACCESS_KEY_ID=${AWS_ACCESS_KEY_ID}
                    export AWS_SECRET_ACCESS_KEY=${AWS_SECRET_ACCESS_KEY}
                    export AWS_REGION=${AWS_REGION}
                    aws ec2 stop-instances --instance-ids ${instanceIds} --region ${AWS_REGION}
                    """
                }
            }
        }
    }
    
    post {
        always {
            echo 'Job completed.'
        }
    }
}
```

## 3. Save and Run the Pipeline

- Click "Save" to save the job configuration.

- Click "Build Now" to execute the pipeline job.

- Monitor the Jenkins console output for job progress and completion.


## Security Considerations

**Credentials Management:** Store sensitive information like AWS credentials securely in Jenkins credentials.

**Pipeline Security:** Avoid hardcoding credentials directly in scripts.

**Access Controls:** Ensure only authorized personnel have access to Jenkins and its configurations.


# Console Output

```
Started by remote host 172.18.236.39
[Pipeline] Start of Pipeline
[Pipeline] node
Running on Jenkins in /var/lib/jenkins/workspace/automation-multiple-ec2-instance
[Pipeline] {
[Pipeline] withEnv
[Pipeline] {
[Pipeline] stage
[Pipeline] { (Run Jenkins Job)
[Pipeline] echo
Running Jenkins job...
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Stop AWS EC2 Instances)
[Pipeline] script
[Pipeline] {
[Pipeline] echo
Stopping AWS EC2 instances...
[Pipeline] readFile
[Pipeline] sh
+ export AWS_ACCESS_KEY_ID=your-key-id-show-here
+ export AWS_SECRET_ACCESS_KEY=your-access key show here
+ export AWS_REGION=your-regin-show-here
+ aws ec2 stop-instances --instance-ids i-087b6044e59352a79 i-0377e7cdf013e4c32 i-0cbe11f2a67404e57 --region us-east-1
{
    "StoppingInstances": [
        {
            "CurrentState": {
                "Code": 64,
                "Name": "stopping"
            },
            "InstanceId": "i-087b6044e59352a79",
            "PreviousState": {
                "Code": 16,
                "Name": "running"
            }
        },
        {
            "CurrentState": {
                "Code": 64,
                "Name": "stopping"
            },
            "InstanceId": "i-0cbe11f2a67404e57",
            "PreviousState": {
                "Code": 16,
                "Name": "running"
            }
        },
        {
            "CurrentState": {
                "Code": 64,
                "Name": "stopping"
            },
            "InstanceId": "i-0377e7cdf013e4c32",
            "PreviousState": {
                "Code": 16,
                "Name": "running"
            }
        }
    ]
}
[Pipeline] }
[Pipeline] // script
[Pipeline] }
[Pipeline] // stage
[Pipeline] stage
[Pipeline] { (Declarative: Post Actions)
[Pipeline] echo
Job completed.
[Pipeline] }
[Pipeline] // stage
[Pipeline] }
[Pipeline] // withEnv
[Pipeline] }
[Pipeline] // node
[Pipeline] End of Pipeline
Finished: SUCCESS

```

## Contributing

Feel free to contribute by opening issues or pull requests!

## License


This `README.md` file provides comprehensive instructions for setting up and using your Jenkins pipeline securely with AWS EC2 instances. Adjust the script and instructions as per your specific setup and requirements before committing it to your GitHub repository.

