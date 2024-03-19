//  GroupName: TeamPRTV (Preethi Ranganathan, Mary Rithika Reddy Gade, Tarun Vinay Gujjar, Vikas Halgar Seetharam)
//  Jenkinsfile defines the pipeline in scm and here, there are 4 stages in
//  this CICD pipeline: Build, Push to DockerHub, Deploying Rancher to single node, Deploying Rancher to Load Balancer

pipeline {
    agent any
    
    environment {
        registry = "tarungujjar/survey"
        registryCredential = 'dockerhub'
        dockerRegistryUrl = "https://index.docker.io/v1/"
        dateTag = new Date().format("yyyyMMdd-HHmmss")
    }
    
    stages {
        stage('Building docker image') {
            steps {
                script {
                    docker.withRegistry("${dockerRegistryUrl}", "${registryCredential}") {
                        def img = docker.build("${registry}:latest")
                    }
                }
            }
        }
        
        stage('Publishing code to Docker Hub') {
            steps {
                script {
                    docker.withRegistry("${dockerRegistryUrl}", "${registryCredential}") {
                        def image = docker.build("${registry}:latest", ". --no-cache")
                        image.push()
                    }
                }
            }
        }
                
        stage('Deploying to single node in Rancher') {
            steps {
                script {
                    sh 'kubectl apply -f swe645-deployment.yaml'
                    sh 'kubectl apply -f swe645-deployment-loadbalancer.yaml'
                    sh 'kubectl rollout restart deployment/swe645-deployment'
                }
            }
        }
    }
    
    post {
        always {
            sh 'docker logout'
        }
    }
}
