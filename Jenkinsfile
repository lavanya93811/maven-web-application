pipeline{
    agent any
    tools{
        maven 'maven_3.9.9'
    }
    environment{
        buildNumber = "${BUILD_NUMBER}"
    }
    stages{
        stage('checkout code from github'){
            steps{
                 git branch:'master' url:'https://github.com/lavanya93811/maven-web-application.git'
            }
        }
        stage('build artifact'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('docker build'){
            steps{
                sh 'docker build -t 954882650728.dkr.ecr.eu-north-1.amazonaws.com/maven-web-application:$buildNumber .'

            }
        

        }
        stage('pushing docker image to ECR'){
            steps{
                sh 'aws ecr get-login-password --region eu-north-1 | docker login --username AWS --password-stdin 954882650728.dkr.ecr.eu-north-1.amazonaws.com'
                sh 'docker push 954882650728.dkr.ecr.eu-north-1.amazonaws.com/maven-web-application:${buildNumber}'
            }
        }
        stage('remove docker images from local'){
        
            steps{
                sh 'docker rmi -f 954882650728.dkr.ecr.eu-north-1.amazonaws.com/maven-web-application:${buildNumber} '

            }
        }
        stage('update image tag in k8 manifiestfile'){
            steps{
                sh "sed -i 's/Build_Tag/${buildNumber}/g' MavenWebApplication.yaml"

            }
        }
        stage('deploy app to EKS cluster')
        steps{
            sh 'kubectl delete deployment webpage-deployment -n production || true'
            sh 'kubectl apply -f MavenWebApplication.yaml'
        }
        
       

    }
}
