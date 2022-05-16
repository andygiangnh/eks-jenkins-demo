
pipeline{

	agent any

	
	stages {
		
		stage('Build') {

			steps {
				sh 'sudo docker build --tag 203343854792.dkr.ecr.ap-northeast-2.amazonaws.com/hello-eks:latest .'
               
			}
		}
        
        stage('login') {

            steps {
		        sh '$(aws ecr get-login --no-include-email)'
            }
        }
		stage('Push') {

			steps {
				sh 'sudo docker push 203343854792.dkr.ecr.ap-northeast-2.amazonaws.com/hello-eks:latest'
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

