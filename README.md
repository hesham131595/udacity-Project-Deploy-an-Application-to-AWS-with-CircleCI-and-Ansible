# Udacity-Project

# I deployed an app to AWS with CircleCI and Ansible. Below are the main goals of the project:

- Explain the fundamentals and benefits of CI/CD to achieve, build, and deploy automation for cloud-based software products.
- Utilize Deployment Strategies to design and build CI/CD pipelines that support Continuous Delivery processes.
- Utilize a configuration management tool to accomplish deployment to cloud-based servers.
Surface critical server errors for diagnosis using centralized structured logging.

# **Dependencies**

- Circle CI - Cloud-based CI/CD service
- Amazon AWS - Cloud services
- AWS CLI - Command-line tool for AWS
- CloudFormation - Infrastrcuture as code
- Ansible - Configuration management tool
- Prometheus - Monitoring tool

# **Project Steps**

1- Create new project in circleci using the github repo

2- **build phase**:

- The goal of a build phase is to compile the source code to check for syntax errors or unintentional typos in code.
- using config.yaml in .circleci  you can add build-frontend and build-backend only in workflow part.

3- **Test phase**:

- Unit tests are one of the many very important building blocks of a system that enables Continuous Delivery.
- using config.yaml in .circleci  you can add test-frontend and test-backend in workflow part.

4- **Analyze Phase**

- checks for known vulnerabilities every time we check in new code
- using config.yaml in .circleci  you can add scan-frontend and scan-backend in workflow part.

5- **Alerts**

- You’re going to add an alert so that botched builds raise a nice wavy red flag.
- CircleCI has auto-enabled the email notification for all failed builds.

- ![image](https://github.com/hesham131595/udacity-Project-Deploy-an-Application-to-AWS-with-CircleCI-and-Ansible/assets/93712347/ac602e72-a30f-47da-9a72-c038d13ff513)





6. Setup - AWS

- Create and download a new key pair in AWS EC2. Name this key pair "udacity" so that it works with the Cloud Formation templates.
- Create IAM user for programmatic access only and copy the access key id and secret access key. You'll also need these credentials to add to CircleCI configuration in the next steps
- Add a PostgreSQL database in RDS that has public accessibility. As long as you marked "Public Accessibility" as "yes", you won't need to worry about VPC settings or security groups. Take note of the connection details, such as:

```docker
Endpoint (Hostname): database-1.ch4a9dhlinpw.us-east-1.rds.amazonaws.com
Instance identifier: database-1 //This is not the database name
Database name: postgres (default)
Username: postgres
Password: mypassword
Port: 5432
```

7- ****Setup - CloudFront Distribution Primer****

- At the very end of the pipeline, you will need to make a switch from the old infrastructure to the new, so we will use CloudFormation and CloudFront to accomplish this. However, for this to work, you must do a few things manually
- Create a **public** S3 bucket with a name that combines "udapeople" and the random string, such as `udapeople-kk1j287dhjppmz437`. If S3 complains that the name is already taken, just choose another random string.
- Manually run the provided **[.circleci/files/cloudfront.yml](https://github.com/udacity/cdond-c3-projectstarter/blob/master/.circleci/files/cloudfront.yml)** template file locally, and use bucket name for the Workflow ID parameter, as shown in the example command below. In the template file, the Workflow ID parameter represents the bucket name.

```docker
cd .circleci/files
aws cloudformation deploy \
            --template-file cloudfront.yml \
            --stack-name InitialStack\
            --parameter-overrides WorkflowID=udapeople-kk1j287dhjppmz437
```

- Upon successful execution, the *cloudfront.yml* template file will create a CloudFront Distribution connected to your existing S3 bucket, and a CloudFrontOriginAccessIdentity

8-  **Configure - CircleCI**

- Add the SSH key (`*.pem`) to CircleCI.
- Add the environment variables to CircleCI by navigating to {project name} > Settings > Environment Variables.

```docker
RUN_ID=asdfghj #random string
AWS_ACCESS_KEY_ID=(from IAM user with programmatic access)
AWS_SECRET_ACCESS_KEY= (from IAM user with programmatic access)
AWS_SESSION_TOKEN= (from IAM user with programmatic access)
AWS_DEFAULT_REGION=(your default region in aws)
TYPEORM_CONNECTION=postgres
TYPEORM_MIGRATIONS_DIR=./src/migrations
TYPEORM_ENTITIES=./src/modules/domain/**/*.entity.ts
TYPEORM_MIGRATIONS=./src/migrations/*.ts
TYPEORM_HOST={your postgres database hostname in RDS}
TYPEORM_PORT=5432 (or the port from RDS if it’s different)
TYPEORM_USERNAME={your postgres database username in RDS}
TYPEORM_PASSWORD={your postgres database password in RDS}
TYPEORM_DATABASE=postgres {or your postgres database name in RDS}
ENVIRONMENT=production
```

![image](https://github.com/hesham131595/udacity-Project/assets/93712347/cd0cb002-9d72-4c90-a5f0-53df7a14bc05)


9- **Deploy the pipeline**

- build triggered by a non-master commit. It should only run the jobs prior to deployment

![image](https://github.com/hesham131595/udacity-Project/assets/93712347/e5942469-5870-420b-b674-80f19cf54d1c)


- build triggered by a master commit. It should only run the jobs prior to deployment

![image](https://github.com/hesham131595/udacity-Project/assets/93712347/b1dcb716-eae1-4f1d-8d18-6e031ea7d75c)


- The app allows you to add new employees. The frontend URL can be obtained through S3 and CloudFront. The backend URL can be seen through EC2.

![image](https://github.com/hesham131595/udacity-Project/assets/93712347/710407ec-9c8d-48b1-9615-7077fdfc8be3)


10- **Configure alerts in Prometheus**
![image](https://github.com/hesham131595/udacity-Project/assets/93712347/c1ab30fc-1667-4739-a8d0-9e87f4f4cd61)

