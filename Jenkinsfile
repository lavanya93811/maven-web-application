pipeline
{
    agent any

    tools
    {
        maven 'maven_3.9.9'
    }

    environment
    {
        buildNumber = "${BUILD_NUMBER}"
    }

    stages
    {
        stage('Checkout Code to Jenkins from GitHub')
        {
            steps()
            {
                git branch: 'master', url: 'https://github.com/lavanya93811/maven-web-application.git'
            }
        }

        stage('Build Artifact using Maven')
        {
            steps()
            {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image')
        {
            steps()
            {
                sh 'docker build -t 954882650728.dkr.ecr.eu-north-1.amazonaws.com/maven-web-application:${buildNumber} .'
            }
        }

        stage('Authenticate and Push Docker Image to AWS ECR')
        {
            steps()
            {
                sh 'aws ecr get-login-password --region eu-north-1 | docker login --username AWS --password-stdin 954882650728.dkr.ecr.eu-north-1.amazonaws.com'
                sh 'docker push 954882650728.dkr.ecr.eu-north-1.amazonaws.com/maven-web-application:${buildNumber}'
            }
        }

        stage('Remove Docker Image from Jenkins Locally')
        {
            steps()
            {
                sh 'docker rmi -f 954882650728.dkr.ecr.eu-north-1.amazonaws.com/maven-web-application:latest:${buildNumber}'
            }
        }

        stage('Update Image Tag in Kubernetes Manifest')
        {
            steps()
            {
                sh "sed -i 's/Build_Tag/${buildNumber}/g' MavenWebApplication.yaml"
            }
        }

        stage("Deploy Application in AWS EKS Cluster")
        {
            steps()
            {
                sh 'kubectl delete deployment webpage-deployment -n production || true'
                sh 'kubectl apply -f MavenWebApplication.yaml'
            }
        }
    }
}

