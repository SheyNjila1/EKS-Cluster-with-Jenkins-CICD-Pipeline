node {

    stage("Git Clone"){

        git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/SheyNjila1/EKS-Cluster-with-Jenkins-CICD-Pipeline.git'
    }

     stage('Gradle Build') {

       sh './gradlew build'

    }

    stage("Docker build"){
        sh 'docker version'
        sh 'docker build -t shey-docker-image .'
        sh 'docker image list'
        sh 'docker tag shey-docker-image jhooq-docker-demo sheynjila/shey-docker-image:shey-docker-image'
    }

    withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'PASSWORD')]) {
        sh 'docker login -u SheyNjila -p $PASSWORD'
    }

    stage("Push Image to Docker Hub"){
        sh 'docker push  sheynjila/shey-docker-image:shey-docker-image'
    }
    
    stage("kubernetes deployment"){
        sh 'kubectl apply -f k8s-spring-boot-deployment.yml'
    }
} 
