pipeline {
    agent any
    environment {
		AWS_ACCOUNT_ID="203343854792"
        AWS_DEFAULT_REGION="ap-northeast-2"
        IMAGE_REPO_NAME="hello-eks"
        IMAGE_TAG="${GIT_COMMIT}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }
   
    stages {
        
        stage('Logging into AWS ECR') {
            steps {
                script {
				sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                }
                 
            }
        }
        
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/andygiangnh/eks-jenkins-demo.git']]])     
            }
        }
  
		// Building Docker images
		stage('Building image') {
			steps{
				script {
				dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
				}
			}
		}
   
		// Uploading Docker images into AWS ECR
		stage('Pushing to ECR') {
			steps{  
				script {
						sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:${IMAGE_TAG}"
						sh "docker push ${REPOSITORY_URI}:${IMAGE_TAG}"
						sh "docker push ${REPOSITORY_URI}:latest"
				}
			}
		}

		// Deploy
		stage('Deploy to EKS') {
			steps{
				script {
					sh "sed -i 's/IMAGE_VERSION/${IMAGE_TAG}/g' ./hello-k8s.yml"
					sh "kubectl apply -f hello-k8s.yml"
				}
			}
		}
	}
}