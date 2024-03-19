//  GroupName: TeamPRTV (Preethi Ranganathan, Mary Rithika Reddy Gade, Tarun Vinay Gujjar, Vikas Halgar Seetharam)
//  Jenkinsfile defines the pipeline in scm and here, there are 4 stages in 
//  this CICD pipeline: Build, Push to DockerHub, Deploying Rancher to single node, Deploying Rancher to Load Balancer 


@NonCPS
def generateTag() {
    return new Date().format("yyyyMMdd-HHmmss")
}
// Pipeline Stages
pipeline {
    environment {
        registry = "tarungujjar/survey"
        registryCredential = 'Docker'
    }
    agent any

    stages{
        //  Build Stage
        stage('Build') {
            steps {
                script {
                    checkout scm
                    sh 'rm -rf *.war'
                    sh 'jar -cvf survey.war -C student_survey/src/main/webapp/ . '
                    // sh 'echo ${BUILD TIMESTAMP}'
                   }
               }
            }
        }
        stage('Docker Build') {
                    tag = generateTag()
                    docker.withRegistry('',registryCredential){
                    def customImage = docker.build("tarungujjar/survey:"+tag)
        }
        // Push to DockerHub Stage
        stage('Push to Docker Hub') {
            steps {
                script {
                    // sh 'echo ${BUILD_TIMESTAMP}'
                    docker.withRegistry('',registryCredential) {
                        def image = docker.build('tarungujjar/survey:'+tag, '.')
                        docker.withRegistry('',registryCredential) {
                            image.push()
                        }
                    }
                }
            }
        }
        // Deploying Rancher to single node
        stage('Deploying Rancher to single node') {
            steps {
                script{
                sh 'kubectl set image swe645-deployment container-0=tarungujjar/survey:'+tag
                }
            }
        }
        // Deploying Rancher to Load Balancer
        stage('Deploying Rancher to Load Balancer') {
            steps {
                script{
                    sh 'kubectl set image swe645-deployment-loadbalancer container-0=tarungujjar/survey:'+tag
                }
            }
        }
    }
}