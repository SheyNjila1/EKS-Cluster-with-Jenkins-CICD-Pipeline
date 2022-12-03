# EKS-Cluster-with-Jenkins-CICD-Pipeline


# 1. Launch Amazon EC2 Instance

### - Ubuntu 20.04   NOTE: Can use any other operating system of your choice 
### - t2.medium
### - Create Security Group and open ports 22, 80, 443, and 8080 with all traffic private (my IP)
### - Key pair for SSH 


# 2. SSH into Amazon EC2 inatance and install and configure the following packages 

## a) Docker
### Use the following commands to install Docker on Ubuntu 20.04 
  ```
  sudo apt update -y 
  sudo apt install docker.io -y
  sudo systemctl start docker
  sudo systemctl enable docker.service
  sudo systemctl status docker.service
  ```
## b) JDK-11 
### Use the following command to install Java 11
  ```
  sudo apt update -y
  sudo apt install openjdk-11-jdk -y
  ```

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
 #### Now, run the following command to use jenkins as root user 
      ```
      sudo su - jenkins
      ```
## d) Install Git, python and ansible 
#### Use the following commands 
    ```
    sudo apt install git -y
    sudo apt install python -y
    sudo apt install python-pip -y
    pip3 install ansible -y
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
## g) Install eksctl: It is used to create AWS EKS clusters
     
     ```
     curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
     sudo mv /tmp/eksctl /usr/local/bin 
     eksctl version
     ```
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
##### b) #Install kops software on ubuntu---->https://github.com/kubernetes/kops/blob/master/docs/install.md
     
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
# 3. Integrate Dockerhub and Github with Jenkins
# 4. Build, deploy and exscute Jenkins CICD Pipline
