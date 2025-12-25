pipeline{
    agent any
    tools{
       maven 'maven_3.9.7'
    }
     environment{
        buildNumber = "${BUILD_NUMBER}"
    }
    
    

    stages{

        stage('Git checkout'){
            steps{
                git branch:'docker_cicd', url:'https://github.com/lavanya93811/maven-web-application.git'
            }
        }
        stage('build artifact'){
            steps{
                sh 'mvn clean package'
            }
        }
         stage('create docker image'){
            steps{
                sh 'docker build -t lavanyatechnologies/docker_cicd:${buildNumber} .'
                            }
        }
         stage('push docker image to dockerhub'){
            steps{
                withCredentials([string(credentialsId: 'Docker_Hub_Password', variable: 'Docker_Hub_Password')]) {
                    sh 'docker login -u lavanyatechnologies -p ${Docker_Hub_Password}'
                }
                sh 'docker push lavanyatechnologies/docker_cicd:${buildNumber} '
            }
        }
        stage('del docker images in local'){
            steps{
                sh 'docker rmi lavanyatechnologies/docker_cicd:${buildNumber}'
            }
        }
        
    }
}
