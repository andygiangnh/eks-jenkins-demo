
pipeline{

	agent any

	
	stages {
		stage('Install') {
			sh 'TAG="$REPOSITORY_NAME.$REPOSITORY_BRANCH.$ENVIRONMENT_NAME.$(date +%Y-%m-%d.%H.%M.%S).$(echo $CODEBUILD_RESOLVED_SOURCE_VERSION | head -c 8)"'
			sh `sed -i 's@CONTAINER_IMAGE@'"$REPOSITORY_URI:$TAG"'@' hello-k8s.yml`
		}

		stage('Build') {

			steps {
				sh 'sudo docker build --tag $REPOSITORY_URI:$TAG .'
               
			}
		}
        
        stage('login') {

            steps {
		        $(aws ecr get-login --no-include-email)
            }
        }
		stage('Push') {

			steps {
				sh 'sudo docker push $REPOSITORY_URI:$TAG'
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

