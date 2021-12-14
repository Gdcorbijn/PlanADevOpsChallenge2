# PlanADevOpsChallenge2

# Considerations Deploying Cloud Formation templates:

    Prerequisites:
        Local environment considerations:
            - This setup assumes the following:
                - You are working on a Windows OS (Recommended Windows 10). Linux setup is coming on our next release!... Stay tuned :) 
                - You have an active AWS account, if not, create one. Instructions: https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/
                - Cloud Formation Templates will be executed by using the root user account of the active AWS account.
            - If not present, download and install Visual Studio Code (VS Code). Instructions: https://code.visualstudio.com/Download
            - If not present, download and install git. Instructions: https://git-scm.com/downloads
            - If not present, download and install aws cli. Instructions: https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
            - If not present download kubectl tool. Instructions: https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/#install-kubectl-binary-with-curl-on-windows            
            - Take into account that git, aws cli and kubectl tools are mandatory but VS Code is just a recommendation, you can use any IDE of your choice compatible with git and yml extension/syntax. Instructions for the entire setup will assume VS Code as your IDE.

# Steps to setup the local repository:

            - Open VS Code
            - Click on "Terminal" upper menu option and select "New Terminal"
            - Step into the folder where you want to create your local repository (i.e. cd C:\Users\[USERNAME]\Documents\MyLocalRepos).
            - Clone the solution repository by running: git clone https://github.com/Gdcorbijn/PlanADevOpsChallenge2.git
            - Select "File" in the upper menu and select "Open Folder". Browse and select the cloned Repository folder (C:\Users\[USERNAME]\Documents\MyLocalRepos\PlanADevOpsChallenge2)
            - Make sure all files found in  https://github.com/Gdcorbijn/PlanADevOpsChallenge2 where cloned into your directory.

# Steps to run DeployBaseNetwork.yml Cloud Formation Template:

            1- Login to your active AWS Account with you root user. Navigate to: https://aws.amazon.com/account/ and click on "Sign In" option on the upper menu. Check the selected Region is "Ohio" in the upper right menu option, otherwise, click on the Region name and select "Ohio" from the displayed regions list. 
            2- Type Cloud Formation on the "Search for services..." textbox in the upper menu.
            3- Select CloudFormation from the options displayed.
            4- Click "Create Stack" button and select "With new resources (standard)" option.
            5- In "Specify Template" section choose "Update a template file". Then click on "Choose file" button.
            6- Browse the folder where the Cloud Formation templates are located (C:\Users\[USERNAME]\Documents\MyLocalRepo>\PlanADevOpsChallenge2).
            7- Choose DeployBaseNetwork.yml and click "Next".
            8- Input:
                - MyVPCStack (or any other name Stack Name) in "Stack Name" parameter.
            9- Input:
                - MyVPC (or any other name for your VPC) in "VPC name" parameter.
                - Any valid CIDR block in "VpcCIDR" parameter (or leave the default value if not sure).
                - Any valid CIDR block in "PrivateSubnetAz1S1CIDR" parameter (or leave the default value if not sure).
                - Any valid CIDR block in "PrivateSubnetAz2S1CIDR" parameter (or leave the default value if not sure).
                - Any valid CIDR block in "PublicSubnetAz1S1CIDR" parameter (or leave the default value if not sure).
                - Any valid CIDR block in "PublicSubnetAz2S1CIDR" parameter (or leave the default value if not sure).
            10- Click "Next" and leave default values for the remaining sections.
            11- Review Stack parameters and click on "Create Strack".
            12- Wait until the Stack is created and review "Outputs" menu option to check all objects created and their respective ids and logical names.
            13- Take note of the following values: ControlPlaneSecurityGroup, PrivateSubnetAz1S1, PrivateSubnetAz2S1, PublicSubnetAz1S1, PublicSubnetAz2S1. You will use these values as parameters to run DeployEKSCluster.yml Cloud Formation Template. 

# Steps to run DeployEKSCluster.yml Cloud Formation Template:

            - Repeat steps 1 to 6 from "Steps to run DeployBaseNetwork.yml Cloud Formation template".
            7- Choose DeployEKSCluster.yml and click "Next".
            8- Input:
                - MyEKSStack (or any other name Stack Name) in "Stack Name" parameter.
            9- Input: 
                - ControlPlaneSecurityGroup into ControlPlaneSecurityGroupID parameter
                - MyEKS (or any other name for your EKS cluster) into EKSClusterName parameter
                - MyEKSIAMRole (or any other name for the IAMRole your EKS cluster will assume) into EKSIAMRoleName parameter
                - PrivateSubnetAz1S1 into PrivateSubnetAz1S1ID parameter
                - PrivateSubnetAz2S1 into PrivateSubnetAz2S1ID parameter
                - PublicSubnetAz1S1 into PublicSubnetAz1S1ID parameter
                - PublicSubnetAz2S1 into PublicSubnetAz2S1ID parameter
            10- Click "Next" and leave default values for the remaining sections.
            11- Review Stack parameters, check the "I acknowledge that AWS CloudFormation might create IAM resources with custom names." checkbox and click on "Create Strack".
            12- Wait until the Stack is created and review "Outputs" menu option to check all objects created and their respective ids and logical names.
            13- Take note of the following values: EKSClusterName. You will use these values as parameters to run DeployEKSClusterNodes.yml Cloud Formation Template.

# Steps to run DeployEKSClusterNodes.yml Cloud Formation Template:

            1- Configure your root users credentials to be able to run aws cli and kubectl commands.
                - Login to your active AWS Account with you root user. Navigate to: https://aws.amazon.com/account/ and click on "Sign In" option on the upper menu.
                - In the upper menu click on your user name and select "Security Credentials".
                - In "Your Security Credentials" menu, expand "Access Keys" section and click on "Create New Access Key" and then Download the csv file. This file contains your accesskey and secret.
                - Open a command line window and run: aws configure. It will prompt for your user accesskey, secret, region and data format.
                    - Input:
                    - Setup the accesskey from your csv file in AWS Access Key ID parameter.
                    - Setup the secret from your csv file in AWS Secret Access Key parameter.
                    - Setup us-east-2 in Default region name parameter.
                    - Setup json in Default output format parameter.
            2- Create an aws key-pair:
                - In the same command line window you opened, run aws ec2 create-key-pair --key-name MyKeyPairTest --query 'KeyMaterial' --output text > MyKeyPairTest.pem
                - Take note of the keypair name setup in the command, you will use it as an EKSNodes template parameter.
            3- Go back to the AWS Console. Check the selected Region is "Ohio" in the upper right menu option, otherwise, click on the Region name and select "Ohio" from the displayed regions list. Type Cloud Formation on the "Search for services..." textbox in the upper menu.
            4- Select CloudFormation from the options displayed.
            5- Click "Create Stack" button and select "With new resources (standard)" option.
            6- In "Specify Template" section choose "Update a template file". Then click on "Choose file" button.
            7- Browse the folder where the Cloud Formation templates are located (C:\Users\[USERNAME]\Documents\MyLocalRepo>\PlanADevOpsChallenge2).
            8- Choose DeployEKSClusterNodes.yml and click "Next".
            9- Input:
                - MyEKSNodesStack (or any other name Stack Name) in "Stack Name" parameter.
            10- Input:
                - Select "ControlPlaneSecurityGroup" Security Group from the dropdown in ClusterControlPlaneSecurityGroup parameter
                - Input MyEKSNodeGroup (or any other name for your EKS cluster nodes) into NodeGroupName parameter
                - Select the keypair you created previously from the dropdown in "KeyName" parameter
                - Select the VPC created previously from the dropdown in "VpcId" parameter
                - Select both private subnets created previosuly from the multiselect parameter "Subnets"
                - Leave the remaining parameters with their default values.
            11- Click "Next" and leave default values for the remaining sections.                
            12- Review Stack parameters, check the "I acknowledge that AWS CloudFormation might create IAM resources with custom names." checkbox and click on "Create Strack".
            13- Wait until the Stack is created and review "Outputs" menu option to check all objects created and their respective ids and logical names.
            14- This template will create an AutoScalingGroup with 2 Instances, you can navigate to the AutoScalingGroup service (as you did for the Cloud Formation service) to monitor instances' status. Once both Instances are in "Healthy" and "InService" state, proceed to the last step of the process: enable these nodes to register to the EKS cluster created with the previous template.
                - Run: aws eks --region us-east-2 update-kubeconfig --name [YourEKSClusterName] - In order to access and manage your EKS cluster.
                - Follow instructions in "To apply the aws-auth ConfigMap to your cluster" from the following AWS support site: https://docs.aws.amazon.com/eks/latest/userguide/add-user-role.html

# Final validation:

Go back to the AWS Console, type EKS on the "Search for services..." textbox in the upper menu and select "Elastic Kubernetes Service" from the displayed options to validate the cluster status, you will see your EKS cluster in "Active" state with 2 nodes registered and the three main infrastructure k8s workloads (coredns, kube-proxy and aws-node) deployed and "Ready".
You can also: 
    - Run: kubectl get pods --all-namespaces - In order to get all pods running in your EKS cluster and their current status, you should see 2 pods per service (coredns, kube-proxy and aws-node) in "Running" state.

# Best Practices considerations:

    - For the sake of simplicity and practicality we used the root account to perform all executions, but this is NOT recommended. You should create a brand new User or Group with the proper permissions (following the "least privilege" principle) to perform the entire exercise.
    - In the same line as the above, we are executing these Cloud Formation templates in the AWS console, being this a repetitive manual task. The recommended approach would be to build out a CI/CD pipeline to have an automated mechanism executing these templates.
    - Regarding the EKS Cluster deployment, we created a Cloud Formation Template for an MVP (Minimum Viable Product) setup. Configurations for in-depth app/infra monitoring and security are recommended for production workloads. Reference https://docs.aws.amazon.com/eks/latest/userguide/security.html for more information.

# Steps to Clean Up the environment:

            1- In the AWS Console, navigate to Cloud Formation service. 
            2- Click on the Stack that manages the resources you want to clean up (i.e. MyVPCStack)
            3- Click on "Delete" button. Confirm by clicking on "Delete Stack".
            4- To confirm your Stack was deleted, go back to the Cloud Formation main view and change the status of the Stacks from "Active" to "Deleted", if successfully deleted, it will appear on the Stacks list.
            - Repeat the process for every Cloud Formation Stack you want to delete. 


    
    

