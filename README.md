🛠 Tools and Technologies Used:
AWS EC2: [Amazon Elastic Compute Cloud] service to launch virtual machines in the Amazon cloud.
MobaXterm: A terminal application for remote connections.
AWS IAM: [Identity and Access Management] service to create users and roles to control access to AWS resources.
AWS CLI: [Command Line Interface] to interact with AWS services from the command line.
eksctl: A command-line tool to create and manage EKS clusters.
CI/CD tools:
Jenkins: An open-source automation server for building, testing, and deploying applications.
Docker: A platform for developing, deploying, and running applications in containers.
Kubernetes CLI (kubectl): A command-line tool to interact with Kubernetes clusters.
Multibranch Scan Webhook Trigger plugin for Jenkins: A plugin to trigger Jenkins pipelines based on events in Git repositories.
What I learned from this project:

Infrastructure Provisioning:

Learned to set up infrastructure on AWS EC2 for a Kubernetes cluster.
Gained experience configuring security groups for secure deployments.
EKS Cluster Management:

Developed skills in creating and managing EKS clusters using eksctl.
Understood configuring worker node configurations for optimal performance.
Learned to integrate IAM OIDC providers for secure access within the cluster.
Containerization with Docker:

Gained hands-on experience with containerizing microservices for portability (if applicable).
Learned best practices for building and managing Docker images (if applicable).
CI/CD Pipeline with Jenkins:

Mastered setting up and configuring Jenkins for automating deployments.
Learned to leverage Jenkins plugins for Docker, Kubernetes, and webhook triggers.
Gained experience in creating Jenkins pipelines that interact with Kubernetes clusters using kubectl commands.
CI/CD Best Practices:

Understood the benefits of continuous integration and continuous delivery for efficient and reliable deployments.
Learned how automated testing within the pipeline ensures code quality before deployment.
Microservices Architecture (if applicable):

Gained a deeper understanding of the microservices architecture and its advantages.
Learned how to deploy and manage individual microservices within a larger application.
Kubernetes Concepts:

Developed an understanding of core Kubernetes concepts like deployments, pods, namespaces, and services.
Gained experience in managing containerized workloads and services within an EKS cluster.
Problem-Solving:

Enhanced your problem-solving skills by troubleshooting configurations, deployments, and potential issues in the CI/CD pipeline.
Additionally, depending on the specific focus of the project, I might have learned more about:

Microservices Architecture: If the project involved deploying microservices, I might have gained a deeper understanding of this architectural style and its benefits.
Kubernetes Concepts: Working with EKS likely involved exposure to core Kubernetes concepts like deployments, pods, namespaces, and services.
Now Let’s begin

Step -1
To commence our project, we must first provision an Amazon Elastic Compute Cloud (Amazon EC2) instance, a fundamental component within our infrastructure framework. This instance will serve as the foundational computing resource upon which we’ll deploy and manage our microservices architecture.

Here’s a step-by-step guide to provisioning an EC2 instance:

1. Launch an EC2 Instance:

Go to the AWS Management Console and navigate to the EC2 service.
Click “Launch Instance”.
2. Choose an AMI:

Search for and select “Ubuntu Server 20.04 LTS”.

3. Configure Instance Type:

Under “Instance Type”, choose “t2.large (2 CPUs and 8 GiB memory)”.
4. Configure Key Pair:

Choose “Create a new key pair” and provide a name (e.g., “ServiceKeyPair”).
Download the key pair file (.pem) and keep it securely. This is essential for SSH access.
5. Configure Storage:

Under “Storage”, select “General Purpose SSD (gp2)” and choose 25 GB for the root volume.
6. Add Security Group Rules:

Inbound Rules: Open the following ports in the security group associated with the instance:
TCP 22: SSH (For secure remote access)
TCP 25: SMTP (For email sending)
TCP 80: HTTP (For web traffic)
TCP 443: HTTPS (For secure web traffic)
TCP 465: SMTPS (For secure email sending)
TCP 6443: Custom TCP (For custom application access)
TCP 27017: Custom TCP (For custom application access)
TCP 30000–32767: Custom TCP Range (For custom application access)

7. Review and Launch:

Review your configuration and ensure all details are correct.
Click “Launch Instances”.
Step -2
I need to connect my EC2 instance from my PC. Let’s proceed by connecting our EC2 instance with Mobaxterm.

Prerequisites:

Public IP Address: Ensure you have the public IP address of your EC2 instance readily available. You can find it in the AWS Management Console under the EC2 service, in the “Instances” section.
MobaXterm Installation: Download and install MobaXterm from their official website: https://mobaxterm.mobatek.net/.
Steps to Connect:

Open MobaXterm: Launch the MobaXterm application.
Create a New SSH Session:
Click on the “Session” button in the top-left corner.
In the “Session settings” dialog, select “SSH” as the protocol.
3. Enter Connection Details:

In the “Remote host” field, paste the public IP address of your EC2 instance.
In the “Specify username” field, enter the username you used during EC2 instance creation. For Ubuntu 20.04 LTS, it’s usually “ubuntu”.
4. Authenticate with Key Pair:

Click the “SSH” tab in the session settings.
Under “Authentication,” choose “Private key file.”
Browse and select the downloaded private key file (.pem) associated with your EC2 instance key pair.
5. Connect

Click “OK” in the session settings dialog.
If your key pair is encrypted, you’ll be prompted for the passphrase. Enter it correctly.
If successful, you’ll be connected to your EC2 instance terminal within MobaXterm.
Additional Notes:

Make sure the security group associated with your EC2 instance has the SSH port (TCP 22) open with your specific IP address as a source for secure access.
MobaXterm offers various features for remote management, including file transfer, terminal emulation, and X11 forwarding.
1. Terminal Window:

Open the terminal window within MobaXterm. This provides the interface for entering commands.
2. Entering the Command:

Type the following command exactly:
sudo apt update
Press Enter to execute the command.
Step — 3
we need to create an IAM user in the AWS Identity and Access Management (IAM) console.

Creating an IAM User:

Go to the AWS Management Console and navigate to the IAM service.
In the navigation pane, select “Users” and then click “Add user”.
Enter a desired username for your new user.
Choose “Programmatic access” for access method (unless you need console access).
Click “Next: Permissions”.
Attaching Managed Policies:

In the “Attach existing policies” section, search for and select the following managed policies:
AmazonEC2FullAccess
AmazonEKS_CNI_Policy
AmazonEKSClusterPolicy
AmazonEKSWorkerNodePolicy
AWSCloudFormationFullAccess
IAMFullAccess

Click “Next: Tags”. (Optional: Add tags for organization purposes)
Click “Next: Review”.
Review the user details and attached policies.
Click “Create user”.
Generating Access Keys:

In the IAM console, select the newly created user.
Click on the “Security credentials” tab.
Click “Create access key”.
Choose “Other” and click “Next”. (Optional: You can set a description for the key)
You’ll see the Access key ID and Secret access key displayed on the screen.
Important: Download the access key information as a CSV file. This is crucial as you won’t be able to view the secret access key again after this step.
Step -4
We will proceed by installing three command-line interfaces (CLIs):

AWS CLI: This CLI facilitates connection to our AWS account, enabling seamless interaction with various AWS services.
kubectl: This CLI connects to Kubernetes, empowering us to manage containerized applications and resources within Kubernetes clusters.
EKS ctl: This CLI tool assists in creating and managing Amazon EKS clusters, streamlining the process of orchestrating containerized applications on AWS.”
1. Create a Directory for Scripts:

Open a terminal window in MobaXterm connected to your EC2 instance.
Run the following command to create a directory named “scripts”:
mkdir scripts
2. Change Directory:

Navigate to the newly created directory:
cd scripts/
Inside the editor, paste the following commands:

# Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install -y unzip
unzip awscliv2.zip
sudo ./aws/install

# Install kubectl
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client

# Install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
Save and exit the editor by typing :wq
Make Script Executable: Make the script executable by running:
sudo chmod +x 1.sh
Execute Script: Execute the script by running:
./1.sh

Configure AWS CLI:

After successful installation, run the following command to configure the AWS CLI with your access key and secret access key obtained from the IAM user you created:
aws configure
Follow the prompts to enter your access key ID, secret access key, default region, and default output format.
Step — 5
Now we create the EKS cluster But Before creating an Amazon Elastic Kubernetes Service (Amazon EKS) cluster, let’s understand what an EKS cluster is:

Amazon EKS (Elastic Kubernetes Service) is a managed Kubernetes service by AWS. It simplifies deploying, managing, and scaling containerized applications on AWS infrastructure. An EKS cluster comprises a managed Kubernetes control plane and worker nodes. It integrates with AWS services for networking, IAM, and logging, providing developers with a reliable and scalable Kubernetes environment.

Now that we have the necessary tools installed (AWS CLI, kubectl, and eksctl), let’s proceed with creating the EKS cluster. We’ll use the eksctl command to automate this process:
eksctl create cluster --name=EKS-1 \
           --region=ap-south-1 \
           --zones=ap-south-1a,ap-south-1b \
           --without-nodegroup
let's breakdown this command
eksctl create cluster: This initiates the creation of a new EKS cluster.

--name=EKS-1: This assigns the name "EKS-1" to our cluster.

--region=ap-south-1: This specifies the region where we want to create the cluster (Asia Pacific South (Mumbai) in this case).

--zones=ap-south-1a,ap-south-1b: This distributes the cluster resources across two availability zones for redundancy.

--without-nodegroup: This indicates that we don't want eksctl to create a managed node group automatically. We'll manage the worker nodes ourselves later.

Running this command will initiate the EKS cluster creation process. It typically takes around 15 minutes as various AWS resources are provisioned. eksctl will also configure your local kubectl configuration to access the newly created cluster automatically.

Next, we need to Run this Command,
eksctl utils associate-iam-oidc-provider \
    --region ap-south-1 \
    --cluster EKS-1 \
    --approve
This is like setting up a secure gateway for your cluster components to access AWS services. Think of it as giving your cluster a special ID card that allows it to request specific permissions from IAM, ensuring everything stays secure and controlled.”

“We can achieve this with the eksctl utils associate-iam-oidc-provider command. This automatically handles the association process, creating an IAM OIDC provider if needed and setting up the necessary trust relationships. It's like taking care of all the technical details behind the scenes."

“By doing this, you’ll enable your cluster pods to securely access AWS services without needing individual credentials. It’s a crucial step for ensuring a secure and well-managed EKS environment.”

Now the cluster is ready, we need to provide an IAM OIDC (OpenID Connect) provider.

eksctl create nodegroup --cluster=EKS-1 \
                       --region=ap-south-1 \
                       --name=node2 \
                       --node-type=t3.medium \
                       --nodes=3 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=DevOps \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access
let’s breakdown this command
This command, eksctl create nodegroup, is like building a team of worker bees for your EKS cluster.

Creating a Managed Node Group:

eksctl create nodegroup: This part tells eksctl to create a new node group, which is essentially a collection of EC2 instances that will serve as worker nodes for your cluster.

--cluster=EKS-1: This specifies that the node group should be associated with your existing EKS cluster named "EKS-1".

--region=ap-south-1: This ensures the node group is created in the same region as your cluster (Asia Pacific South (Mumbai) in this case).

Configuring the Node Group:

--name=node2: This gives your node group a name, "node2", for easy identification.

--node-type=t3.medium: This defines the type of EC2 instances to use for the worker nodes. Here, they'll be t3.medium instances.

--nodes=3: This specifies the initial number of worker nodes to create, which is 3.

--nodes-min=2: This sets the minimum number of nodes the group should always maintain, ensuring at least 2 are always available.

--nodes-max=4: This defines the maximum number of nodes the group can scale up to, allowing for flexibility based on workload demands (up to 4).

--node-volume-size=20: This allocates 20 GiB of storage space for each worker node.

Additional Settings:

--ssh-access: This enables SSH access to the worker nodes using the specified public key (DevOps in this case).

--managed: This indicates you want a managed node group, meaning AWS automatically manages scaling and updates for the worker nodes.

--asg-access: This grants access to the Auto Scaling group managing the worker nodes.

--external-dns-access: This enables automatic DNS record creation for your worker nodes.

--full-ecr-access: This grants your node group full access to your Amazon Elastic Container Registry (ECR) for container image deployment.

--appmesh-access: This grants access to AWS App Mesh for service mesh management within your cluster.

--alb-ingress-access: This allows using Application Load Balancers for ingress traffic to your workloads.


Now we need to install “java”.

sudo apt install openjdk-17-jre-headless

Step — 6
• Let’s walk through installing Jenkins on your EC2 instance:
Installing Jenkins with a Script:

Creating the Script: We’ll create a script named “olo.sh” that automates the installation. Imagine this script as a recipe for setting up Jenkins
Editing the Script: Open a text editor on your EC2 instance and paste the following commands into the “olo.sh” file:
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
Making the Script Executable:

Once you’ve saved the script, run this command to grant it permission to be executed:
sudo chmod +x olo.sh
This is like giving the script the green light to run its commands.
Running the Script:

Now, simply execute the script by typing:
./olo.sh
Now Find your EC2 instance’s public IP in the AWS Management Console.

In a web browser, type the public IP followed by “:8080” (e.g., http://123.456.78.90:8080).
On the Jenkins setup page, “install suggested plugins” for extra features.

Now Create Admin User:

Alright, let’s get Docker set up on our EC2 instance. First, we’ll install Docker by running this command:
sudo apt install docker.io -y
This will ensure we have Docker ready to go. Next up, we need to tweak some permissions. Run this command:

sudo chmod 666 /var/run/docker.sock
This will make it easier for us to interact with Docker without needing root access every time.

And that’s it! Docker should be good to go on our EC2 instance now.”

Step — 7
- Now we need to install some plugins to do that go to the Jenkins Dashboard.
Open your jenkins dashboard
Go to Manage Jenkins > Manage Plugins.
Search for and Select Plugins:
Docker

Docker Pipeline

Kubernetes

Kubernetes CLI

Install Selected Plugins.
Restart Jenkins to apply changes.

-> Now let’s get to Jenkins tools section and setup the Docker:
Manage Jenkins -> Tools

Find Docker Installation:
Under Docker installations, click “Add Docker” and give it a name” Docker”

Select “Install automatically” and choose “Download from docker.com”.

Leave the “Version” field as “latest” to get the newest stable version of Docker.

Click “Save” to add this Docker installation.

Apply the Changes:


Step — 8
Now, let’s add Docker Hub credentials to Jenkins. Make sure the credential name matches the one specified in the GitHub repository.


Now we need to add “credentials” in the Jenkins that interact with Git source code.
Login to Jenkins dashboard.
Navigate to “Manage Jenkins” -> “Credentials” -> “Global credentials (unrestricted)”.
Click “Add Credentials” and choose “Username with password”.
Add Credential. For the username, enter ‘raj’ and provide the password.
Then add the ID name, Ensure the ID matches the one in the GitHub repository (Docker-cred).
Now we need to add another credential to Jenkins’ global credentials. This time it’s for Git.
1. Generate GitHub Personal Access Token:

Log in to your GitHub account.
Navigate to Settings -> Developer settings -> Personal access tokens.
Click on Generate new token.
Grant necessary access permissions for your Jenkins jobs (e.g., repo, admin:repo_hook).
Generate the token and copy it securely (you won’t be able to see it again after this step).
2. Add Credentials in Jenkins:

Open your Jenkins dashboard and log in with admin credentials.
Navigate to Manage Jenkins -> Credentials -> System -> Global credentials (unrestricted).
Click on Add Credentials.
In the Secret field, paste the copied GitHub personal access token.
Enter your GitHub username (Exp — bindheyashrita)
In the ID and Description fields, enter a descriptive name (e.g., “git-cred”). This helps you identify these credentials later.
Click OK to save the credentials.

Step — 9
Now let's add a WebHook Plugin
Navigate to Jenkins dashboard > Manage Jenkins > Plugins > Available Plugins. Search for ‘Multibranch Scan Webhook Trigger’, select it, and install.



Now let’s create a Multibranch Pipeline in Jenkins, we will following these steps:

Navigate to Jenkins Dashboard:
2. Create New Item:

Click on the ‘+’ icon and select ‘New Item’.
3. Name and Select Pipeline Type:

Enter the name as “MicroService E-Commerce”.
Choose ‘Multibranch Pipeline’ as the project type.
4. Configure Branch Sources:

In the Branch Sources section, click ‘Add source’ and select ‘git’.
Specify the Git repository URL containing the code. (https://github.com/bindheyashrita/11-microservice.git)
Select ‘git-cred’ as the credential for accessing the Git repository.
5. Build Configuration (Jenkinsfile):


In the “Script Path” field, leave the default value “(none)” as Jenkins will automatically search for a file named “Jenkinsfile” in the Git repository root directory. This file contains the pipeline code for building and deploying your application.
6. Scan Multibranch Pipeline Triggers:

Check the box next to “Scan Multibranch Pipeline Triggers.”
Select “GitHub webhooks” as the trigger type.
In the “Trigger token” field, enter a unique string (e.g., “raj”). This token will be used in the webhook URL to identify authorized triggers.
7. Webhook URL:

Click the question mark next to “Trigger phrase” for guidance on configuring the webhook URL in your Git repository settings. You’ll need to replace TOKENHERE with the trigger token you entered earlier in the "Trigger token" field. The complete webhook URL should look something like this:

JENKINS_URL/multibranch-webhook-trigger/invoke?token=TOKENHERE
Note: Replace JENKINS_URL with the actual URL of your Jenkins instance (e.g., http://your-jenkins-server:8080).

8. Apply Configuration:

Click the Apply button to save the multibranch pipeline configuration.
Step — 10
Setting up GitHub Webhook for Jenkins Integration
To set up a GitHub webhook for Jenkins integration, follow these steps:

Navigate to GitHub Settings:
Go to your GitHub repository settings.
2. Access Webhooks:

Select the ‘Webhooks’ option from the settings menu.
3. Add Webhook:

Click on ‘Add webhook’ to create a new webhook.
Configure Payload URL:
Enter the Jenkins webhook URL in the ‘Payload URL’ field.
Replace ‘JENKINS_URL’ with your Jenkins server URL.
Replace ‘TOKENHERE’ with the generated token.
Example: JENKINS_URL/multibranch-webhook-trigger/invoke?token=TOKENHERE
4. Set Content Type:

Choose ‘application/json’ as the content type.
5. Select Events:

Choose ‘Just the push event’ to trigger the webhook only when code is pushed.

6. Activate Webhook:

Ensure the webhook is active by checking the ‘Active’ option.
7. Add Webhook:

Click ‘Add webhook’ to save the configuration.
Now that our GitHub triggers the pipeline, we have a total of 11 pipelines. Each pipeline will build the Docker image for its corresponding microservice and push it to the Docker Hub repository


Now that the CI part is complete, we transition to the CD phase. Here, we’ll create a service account, assign a dedicated role to it, and then carry out the deployment using this service account.

Step — 11

Now we are creating a Kubernetes Service Account for Jenkins Deployments

On your EC2 instance, create a file named svc.yml using the command:
vi svc.yml
Paste the following code into the file:
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
Save the file by pressing Esc, then typing :wq, and finally pressing Enter.
Create Namespace:

Execute the command to create the namespace:
kubectl create namespace webapps
Apply Service Account Configuration:
Apply the configuration from the svc.yml file:
kubectl apply -f svc.yml

- Now we creating the service account, role, and binding
Creating Service Account:

Create a file named svc.yml on your EC2 instance and paste the following code:
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
Create a namespace first
kubectl create namespace webapps
Save the file

kubectl apply -f svc.yml
2. Creating Role:

Create a file named role.yml and paste the following code:
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
      - ""
      - apps
      - autoscaling
      - batch
      - extensions
      - policy
      - rbac.authorization.k8s.io
    resources:
      - pods
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
Save the file and execute:
kubectl apply -f role.yml
3. Binding Role to Service Account:

Create a file named bind.yml and paste the following code:
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role
subjects:
  - namespace: webapps
    kind: ServiceAccount
    name: jenkins
Save the file and execute:

kubectl apply -f bind.yml
Next, Once the role is bound to the service account, the next step is to generate a token for this service account, which will be used for authentication.

we need to create a token for our service account to enable authentication. Let’s create a file named ‘sec.yml’ and add the following code:

apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins
Don’t forget to include the namespace ‘webapps’ when applying this configuration. Once done, execute the command:

kubectl apply -f sec.yml -n webapps
This token will allow our ‘jenkins’ service account to authenticate for deployments.

after binding the role to the service account, the next crucial step is to obtain a token for authentication. We achieve this by executing the following command:

kubectl describe secret mysecretname -n webapps

and copy token so that it will use.

Step — 12
Next, go back to the Jenkins dashboard. We need to create a CD pipeline. Now, create a dummy pipeline for generating the pipeline syntax of the main branch.

Create CD Pipeline in Jenkins:
Navigate to Jenkins dashboard and create a new item.
Name it “Dummy” and select pipeline type.

In advanced project options, choose pipeline and select script.
Go to pipeline syntax

pipeline syntax → overview -> simple step ->withkubecrenditals: configure kubenetes CLI (kubectl) with multiple credtials

Credentials -> add -> Jenkins (For generating)




Add Credentials:
Navigate to Jenkins Credentials and select ‘Add Credentials’.
Choose ‘Domain’ as ‘Global credentials unrestricted’.
Select ‘Kind’ as ‘Secret text’.
Set ‘Scope’ to ‘Global (Jenkins, nodes, items, all child items, etc.)’.
In the ‘Secret’ section, paste the token copied from the service account.
Set ‘ID’ and ‘Description’ as ‘k8-token’.
Now add
2. Now in the credential section select → k8-token


now we fill the Kubernetes API endpoint
3. Configure Kubernetes API Endpoint:

Obtain the Kubernetes API endpoint from your EC2 instance > EKS Cluster > EKS1 > Overview.

Now copy the API server endpoint link

and paste it on Kubernetes API Endpoint


Specify ‘clustername’ as ‘EKS-1’ and ‘namespace’ as ‘webapps’.

4. Generate Pipeline Script:

Generate the pipeline script and copy the code.
Paste the code into the steps section of your pipeline.

5. Go to pipeline syntax and paste the code


Now, we put the command

Next, we’ll add another pipeline script titled “Verify Deployment”.


6. Now that both scripts are prepared, here’s the twist: instead of applying them directly, we’ll use them elsewhere. Copy both scripts and navigate to the main microservice directory in the GitHub project’s — main — microservice directory. Create a new file named ‘Jenkinsfile’ and paste the copied script into it.


Okay, here we need to make changes in the code
- removing the sleeping mode
- adding svc
pipeline {
    agent any

    stages {
        stage('Deploy To Kubernetes') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://9F39F577334FF23706994135261985F2.gr7.ap-south-1.eks.amazonaws.com']]) {
                    sh "kubectl apply -f deployment-service.yml"
                    
                }
            }
        }
        
        stage('verify Deployment') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'EKS-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://9F39F577334FF23706994135261985F2.gr7.ap-south-1.eks.amazonaws.com']]) {
                    sh "kubectl get svc -n webapps"
                }
            }
        }
    }
}

Now commit the changes

Let’s tidy up by removing the dummy pipeline we created earlier


Okay, let’s check whether our pipeline was created or not, which we previously committed changes on GitHub?


Go to Jenkins -> Dashboard -> Microservice E-Commerce -> main (where we committed changes in GitHub).


Now its deploying


Now copy the ID and paste it into the browser to see the site.

Final site


