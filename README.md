# Onica Webservers

The cicd-pipeline.yml is a cloud formation template that provisions a CodePipeline Pipline that will provision the loadbalanced, scalable, highly available website defined in the webservers.yml.

## Setup

### CI/CD

Use the following steps to set up a CD/CD pipeline.

- Download the CloudFormation template cicd-pipline.yml and use it to create a stack either through the AWS Console, or using the cli

**Note the stack name is important and should be cicd-pipeline**

```bash
$ aws cloudformation create-stack --stack-name cicd-pipeline --template-body file://cicd-pipeline.yml --capabilities CAPABILITIES_IAM
```

- Check the output for for the stack for the CodeCommit URL's.
- Configure the webserver-configuration.json with the name of an existing SSH key pair.
- Push the webservers-configuration.json and webservers.yml files to the master branch **in a folder called templates** to trigger CodePipeline and automatically provision the AWS resources.
- Check the output of the Deploy section for the loadbalancer URL used to access the web site.

### Manually Provision Resources

Use the following to forego a pipeline and provision the AWS resources using CloudFormation manually.

- Edit the webservers.yml file as required and use it to create a CloudFormation stack either through the AWS Console, or using the cli

**Note the stack name is important and should be cicd-pipeline**

```bash
$ aws cloudformation create-stack --stack-name cicd-pipeline --template-body file://webservers.yml --capabilities CAPABILITIES_IAM
```

- Check the output of the CloudFormation stack for the loadbalancer URL used to access the web site.

## Testing

To test the autoscaling a stress application has been configured to be installed in the launch configuration. Log into the ec2 instance and execute the following command.

```bash
$ sudo stress --cpu 4 --timeout 600
```

After severl minutes a cloud watch alarm will indicate that the Average CPU exceeds the expected threshold of 25% and 2 new EC2 instances will be launched to maintain the cpu utilization at 25%.

Approximately 15 minutes after the stress application times out, or is stopped, the desired EC2 instances will return to 2, and the excess instances will be terminated automatically.

**Note the web_server_security_group is configured to only allow access to one IP address on port 22, to access the instance a rule will need to be added.**
