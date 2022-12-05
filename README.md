# EKS-Cluster-with-Jenkins-CICD-Pipeline


# 1. Launch Amazon EC2 Instance

### - Ubuntu 20.04   NOTE: Can use any other operating system of your choice 
### - t2.medium
### - Create Security Group and open ports 22, 80, 443, and 8080 with all traffic private (my IP)
![Jenkins-SG](https://user-images.githubusercontent.com/96470430/205450260-b73dd789-7fbc-4dd8-8d55-cd9b9c18729c.PNG)

### - Key pair for SSH 
![Keypair](https://user-images.githubusercontent.com/96470430/205450352-693d74a1-2139-4f5c-83bb-18a675d1210b.PNG)

### EC2 Instance created

![EC2-Ubuntu](https://user-images.githubusercontent.com/96470430/205451032-05bb077b-80ca-469a-9b5b-cd9ef7860c1a.PNG)


# 2. SSH into Amazon EC2 instance and install and configure the following packages. 
#### Here I use PowerShell but there are other options like Putty, MobaXtern, Termius, EC2 Instance Connect, SSM etc

![SSH-to-Jenkins-Server](https://user-images.githubusercontent.com/96470430/205451173-37dcc714-458d-4a5d-9267-bcbfd298a13f.PNG)

## a) Docker
### Use the following commands to install Docker on Ubuntu 20.04 
---
sudo apt update -y 
  sudo apt install docker.io -y
  sudo systemctl start docker
  sudo systemctl enable docker.service
  sudo systemctl status docker.service
---
  
  
#### Make sure Docker is up and running

![Docker-up and running](https://user-images.githubusercontent.com/96470430/205451533-f64f83b7-20c1-4c64-8c7e-ec5c9a033fd5.PNG)

## b) JDK-11 
### Use the following command to install Java 11
```
  sudo apt update -y
  sudo apt install openjdk-11-jdk -y
  java --version 
```
#### Check the Java Version that has been installed:
![image](https://user-images.githubusercontent.com/96470430/205451734-2d62dd6b-c1a5-43f7-9dd7-cb37ce8d3c44.png)


 ## c) Jenkins
 ### i) Use the following Command to download and install Jenkins
 ```
     
    wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
    sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
    sudo apt update -y
    sudo apt install jenkins -y
    sudo systemctl start jenkins
    sudo usermod -aG docker jenkins
    sudo systemctl restart docker.service
    sudo service jenkins status
```
 ### ii) Add Jenkins user to Docker group
 #### Add jenkins user to Docker group to allow Jenkins to access Docker and build application Docker images. 
 
```
      sudo usermod -aG docker jenkins
```
 ### iii) Add jenkins to sudoers
 #### It is important to add jenkins user as an administrator and also as NOPASSWD so that no password will be asked during the pipeline run/execution.
 
 ```
      sudo vim /etc/sudoers
 ```
 #### add the following line under sudoers group section 
```
      jenkins ALL=(ALL) NOPASSWD: ALL 
```
  ##### jenkins now has admin previleges
  ![Sudoers](https://user-images.githubusercontent.com/96470430/205452567-8a0a04dd-cbe8-49ac-8bd9-f9cc962ef077.PNG)


 #### Now, run the following command to use jenkins as root user 
```
      sudo su - jenkins
```
 ##### Make sure Jenkins server is active(running) as shown below:
 
 ![Jenkins-active-running](https://user-images.githubusercontent.com/96470430/205452962-0b819b7a-f5ef-41f8-96bf-6cb617667984.PNG)

 Proceed to access  the Jenkins through the browser using the public IP of the Jenkins server(EC2 Instance) and port 8080 http://PublicIP:8080
 ![Jenkins-access-browser](https://user-images.githubusercontent.com/96470430/205453144-c42d21be-1f3b-41f9-93eb-3415bf901a24.PNG)

#### Obtaining initialAdminPassword password if accessing for the first time. Copy the ***/var/lib/jenkins/secrets/initialAdminPassword***  and cat to display the password that will be used to access Jenkins

```
    cat /var/lib/jenkins/secrets/initialAdminPassword
```
    
![JenkinsInitialPassword](https://user-images.githubusercontent.com/96470430/205453498-fa130886-3633-431e-87ef-1b3dd924f3a4.PNG)
 
 #### Install Suggested Jenkins Plugins: 
 
 ![Install Suggested Plugins](https://user-images.githubusercontent.com/96470430/205453722-624c3182-f020-404c-a862-e8fadcfd2bf9.PNG)

![Suggested-Plugins installed](https://user-images.githubusercontent.com/96470430/205453729-a62d978f-9f78-4ed9-9d17-9001bc46c366.PNG)

##### Customize Password
![UserInfor](https://user-images.githubusercontent.com/96470430/205453794-8762b248-bacc-4661-ac79-92e6afbbc6c3.PNG)
##### Instance configuration
![Instance Configuration](https://user-images.githubusercontent.com/96470430/205453915-b2a2585a-f9ca-43a8-8674-f47f315db982.PNG)
##### Jenkins is now ready to use:
![Jenkins ready](https://user-images.githubusercontent.com/96470430/205453956-5796f6a0-ec6c-4947-93b0-c538cd64ab27.PNG)

##### Display of Jenkins Dashbaord
![Jankins Dashboard](https://user-images.githubusercontent.com/96470430/205454108-f3bed9ff-c87c-4e6a-a30d-56274d9c41ab.PNG)

## d) Install Git, python and ansible 
#### Use the following commands 
```
    sudo apt install git -y
    apt install python-is-python3 -y  # sudo apt install python -y is obsolate or not just working here
    sudo apt install python-pip -y
    # pip3 install ansible -y
```
## e) Install AWS CLI
#### Install awscli---->https://docs.aws.amazon.com/cli/v1/userguide/install-linux.html 
```
    sudo apt install wget -y
    sudo apt update -y
    sudo apt install unzip 
    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip
    sudo ./aws/install
```
    #### Verify that AWS CLI is properly installed:
    
   ![AWS-CLI](https://user-images.githubusercontent.com/96470430/205454839-86dfd495-5d21-4b50-8cd3-f5a5b61c4ef8.PNG)

#### Configure AWS CLI: This configuration is needed so that AWS CLI can authenticate and communicate with Amazon AWS
```
    aws configure
    
```
    
   ![AWS configure](https://user-images.githubusercontent.com/96470430/205456027-58ca2289-5360-45bd-9ef7-4d3ca614b1c6.PNG)

## f) Install Kubectl
##### https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/#install-kubectl-binary-with-curl-on-linux  
 ```
 curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
    echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    # Use any of the following to very if kubectl is working 
    kubectl
    kubectl version --client
    kubectl version --client --output=yaml
 ```
#### Kubectl is installed
![Kubctl-installed and version confirmed](https://user-images.githubusercontent.com/96470430/205454986-f76b3ea1-6517-4f54-9292-2bddcc380cc6.PNG)

## g) Install eksctl: It is used to create AWS EKS clusters
     
 ```
     curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
     sudo mv /tmp/eksctl /usr/local/bin 
     eksctl version
 ```
 ##### eksctl installed:
![eksctl-installed](https://user-images.githubusercontent.com/96470430/205455126-db4b27ec-8e9e-43c1-bbae-cd150d676a7d.PNG)

## NOTE!!!! Other tools not needed in this specific project
##### a) Install minikube---->https://medium.com/@ruslanfg/getting-started-fast-with-kubernetes-39097c9fede2
     
 ```
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
    echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    sudo curl -sLo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
    sudo mv ./minikube /usr/local/bin                           
    sudo chmod a+x /usr/local/bin/minikube
```
##### b) Install kops software on ubuntu---->https://github.com/kubernetes/kops/blob/master/docs/install.md
     
```
    sudo apt install wget -y
    sudo wget https://github.com/kubernetes/kops/releases/download/v1.18.2/kops-linux-amd64
    sudo chmod +x kops-linux-amd64
    sudo mv kops-linux-amd64 /usr/local/bin/kops
```
##### c) # Setup Jenkins to start at boot
    
``` 
      sudo apt install git -y
      sudo apt install python -y
      sudo apt install python-pip -y
      pip3 install ansible -y
```

# 3. Setup Gradle: It is a build tool for compilation and building the JAR file  
##### Goto -> Manage Jenkins -> Global Tool Configuration -> Gradle
![Gragle Setup](https://user-images.githubusercontent.com/96470430/205455608-f108bb84-f11c-48c9-99b3-adff5877be25.PNG)

# 4. Create an AWS eks cluster using eksctl
##### The following compoents of the command are required: 
#### - Name of the cluster : --name shey-eks-cluster
#### - Version of Kubernetes : --version 1.24
#### - Region : --name eu-central-1
#### - Nodegroup name/worker nodes : worker-nodes
#### - Node Type : t2.micro
#### - Number of nodes: -nodes 3
#### Command
```
eksctl create cluster --name shey-eks-cluster --version 1.24 --region us-east-1 --nodegroup-name worker-nodes --node-type t2.micro --nodes 3

```
### Cluster Completely created as shown in CLI 
![Cluster Creation Completed](https://user-images.githubusercontent.com/96470430/205457497-60587abf-2f6c-4f6f-b507-b81cb1541dde.PNG)

### Cloudformation stacks created as the Networking component for the cluster 
![Cloudformation Stacks](https://user-images.githubusercontent.com/96470430/205457585-bef9b179-f5b1-4a06-8ad9-a3e72304ffd3.PNG)
### Cluster VPC Created with al networking compenets
![Cluster-VPC-Created](https://user-images.githubusercontent.com/96470430/205457656-aaa5fff8-edfd-4858-b018-26d46aaa6e4d.PNG)
### Worker nodes running in EC2 console. Master node is managed and not displayed
![Workernodes instances](https://user-images.githubusercontent.com/96470430/205457789-9b599654-160f-46c6-8fa9-b7a5c2487cea.PNG)
### EKS cluster is created
![EKS-Cluster-created](https://user-images.githubusercontent.com/96470430/205457890-4a99c74b-fca4-4af0-939f-19e935b50804.PNG)

### Cluster showing three worker nodes as expected
![Cluster with three worker nodes](https://user-images.githubusercontent.com/96470430/205457884-00061def-fe6c-4699-a6b4-c437051384bb.PNG)



# 5. Integrate Dockerhub and Github with Jenkins
### GitHub and DockerHub credentials are necesaasry to accomplish this task
#### ###Goto -> Jenkins -> Manage Jenkins -> Manage Credentials -> Stored scoped to jenkins -> global -> Add Credentials###
#### DockerHub Credentials
![DockerHubCredentials](https://user-images.githubusercontent.com/96470430/205459482-17e0b334-9538-46a4-a669-374555662094.PNG)
#### GitHub Credentials : GitHub Username and password into Jenkins
![GitHub Crdentials](https://user-images.githubusercontent.com/96470430/205459637-57abdca9-6bde-4fff-9b62-f5f9d2143756.PNG)

# 6. Jenkins Stages for Pipepline
#### a) Checkout the GitHub Repository
```
stage("Git Clone"){

        git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/rahulwagh/k8s-jenkins-aws'
    }
```
#### b) Gradle Compilation: This compiles and builds the application using 
```
stage('Gradle Build') {
    sh './gradlew build'
}

```
#### c) Create and Push DOcker container to DOcker Hub . This only possible after successful compilation and build 
'''
stage("Docker build"){
    sh 'docker version'
    sh 'docker build -t jhooq-docker-demo .'
    sh 'docker image list'
    sh 'docker tag shey-docker-image sheynjila/shey-docker-image:shey-docker-image'
}

stage("Push Image to Docker Hub"){
        sh 'docker push  sheynjila/shey-docker-image:shey-docker-image'
}

'''
d) Kubernetes Deployment

```
stage("kubernetes deployment"){
  sh 'kubectl apply -f k8s-spring-boot-deployment.yml'
}
```

# 5. Build, deploy and execute Jenkins CICD Pipline

