
pipeline{

	agent any

	
	stages {
		
		stage('Build') {

			steps {
				sh 'sudo docker build --tag 203343854792.dkr.ecr.ap-northeast-2.amazonaws.com/hello-eks:latest .'
               
			}
		}
        
        stage('Push') {

			steps {
				script{
                        docker.withRegistry('https://203343854792.dkr.ecr.ap-northeast-2.amazonaws.com', 'ecr:ap-northeast-2:aws-credentials') {
                    app.push("${env.BUILD_NUMBER}")
                    app.push("latest")
                    }
                }
			}
			
		}

        stage('eks deploy') {

			steps {
				sh 'kubectl apply -f hello-k8s.yml'
                sh 'kubectl rollout restart deployment hello-k8s'
			}
		}
	}

	post {
		always {

            		cleanWs()
			
            		echo "done"
		}
	}

}

