# Containerized web application using Docker
Creating a Docker image and pushing it to DockerHub:
- Ensure that Docker is installed on our machine and create an account on https://hub.docker.com/.
- In your eclipse environment, create a Dockerfile without any extension (this name is specific for Docker's use).
- Compile your project files within the code editor, ensuring the resultant war file is inthe same directory as your Dockerfile. Assume "survey" as your Tomcat application's name.
- Write the following in your Dockerfile:
  FROM tomcat:latest
  COPY survey.war /usr/local/tomcat/webapps/
- Open the command prompt and set the path of the directory to where the Dockerfile resides.
- Build the Docker image with the command docker build --tag survey:0.1.
- In order to check if the image has been created, run the command docker images
- Check the image's correctness by running docker run -it -p 8182:8080 survey:0.1
- Visit http://localhost:8182/survey via a web browser.
- Log into Docker Hub through the terminal:
  docker login user_name.
- Rename your Docker image to align with the docker tag command:
  docker tag survey:0.1 tarungujjar/survey:0.1. (where survey is the docker image name)
- The docker image must be pushed to the docker hub using the command:
  docker push user_name/survey:0.1.
- The Docker image is publicly accessible and ready for use in Rancher Deployment.


# Orchestrated deployment using Kubernetes
Installing docker and Rancher (to setup kubernetes cluster) on AWS EC2.
- Two EC2 instances were launched using AWS Learner Lab.
- The instances were created with default configurations, with specific choices made during creation:
- The instance type selected was t2.medium, and the security group was configured to include TCP port ranges 80, 8080, 443, in addition to the default port 22.
- The storage configuration was increased to 1x28 GB.
- Two Elastic IPs were created for flexible cloud computing and associated with the EC2 instances.
- After launching, both EC2 instances were connected, and a routine update was performed using the command: sudo apt-get update.
- Docker was installed on both instances using the command:
  sudo apt install docker.io.
- Rancher was installed on the first instance using the command:
  sudo docker run --privileged=true -d --restart=unless-stopped -p 80:80 -p 443:443 rancher/rancher.
- The Container ID was retrieved with: sudo docker ps.
- The Container ID was then utilized to generate a Rancher login password with the command:
  sudo docker logs [Container ID] 2>&1 | grep "Bootstrap Password:", resulting in a randomly generated password.
- The public IPv4 DNS of the instance with Rancher installed was accessed, and the generated password was used to log into the Rancher cluster management portal, where the password was changed.
- Creating a cluster was straightforward, involving naming the cluster and proceeding.
- ETCD, control panel, and worker nodes registration was checked before moving forward.
- A command has been generated which must be copied to the other EC2 instance. This instance will serve as our worker node, from which we'll pull the image. The command is as follows:
  sudo docker run -d --privileged --restart=unless-stopped --net=host -v /etc/kubernetes:/etc/kubernetes -v /var/run:/var/run rancher/rancheragent:v2.7.1 --server https://ec2-18-212-81-132.compute-1.amazonaws.com --token phncs4hc9qmmp7rjp9hjbmxlgwxhb927t5l7hcxr7m7wj8tgfwj2gp --cachecksum 488b84e64620fe0c081af13ce9568c2d213047264e67987f980b76a6463501ec --etcd --controlplane --worker
- We receive an immediate confirmation: the worker node is successfully connected.
- Provisioning may take some time, but once completed, the cluster becomes active.
- Upon cluster activation, navigate to the cluster and access the "Deployments" tab within the workload section on the left-hand side.
- Click on "Create" and proceed to generate a new deployment object with 3 replicas, configured as nodeport. You'll need to specify the image from Docker Hub for this deployment.
- Additionally, create another deployment for the load balancer.
- Once the deployments are active, you should be able to access your application by clicking on the NodePort 8080 link under the services of the deployment. Append your WAR file name to the end of the URL.


# Built a CI/CD pipeline using Jenkins
Steps to install and setting up Jenkins
- Set up an EC2 Instance on AWS specifically for Jenkins.
  a. Provision an EC2 Instance dedicated to Jenkins on AWS.
  b. Allocate an Elastic IP for flexible cloud computing and associate it with this EC2 instance.
  c. Establish a connection.
- Install Docker and Jenkins on the EC2 Instance using the following commands:
  Install Docker:
  a. $ sudo apt-get update
  b. $ sudo apt install docker.io
  Install Jenkins:
  a. Follow the installation steps outlined in this link: https://pkg.jenkins.io/debian/
- Confirm the installation status of Jenkins by executing ‘systemctl status jenkins’ and accessing a web browser using <Public IP of the Instance> :8080 that is 3.231.140.222/:8080.
- Deploy kubectl on the Jenkins instance and log in as the Jenkins user using the provided commands:
  a. Install snapd: $ sudo apt install snapd
  b. Install kubectl via Snap: $ sudo snap install kubectl --classic
  c. Switch to Jenkins user: $ sudo su jenkins
- Within Rancher, navigate to your Cluster, and copy the content of the Kubeconfig file. Paste it into the /home/Jenkins/.kube/config file. Ensure the existence of the ‘.kube’ folder in the Jenkins home directory on the EC2 console. Execute the following commands:
  a. Create the .kube folder: $ mkdir /home/Jenkins/.kube
  b. Edit the config file: $ vi /home/Jenkins/.kube/config
  c. Paste the Kubeconfig file content in the vi editor.
- Validate the functionality of kubectl by executing ‘kubectl config current-context’ to retrieve the name of your cluster.
