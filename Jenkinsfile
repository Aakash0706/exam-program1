pipeline {
	agent any
	environment {
		IMAGE_NAME="jaiswalakash/program1"
		IMAGE_TAG="v${BUILD_NUMBER}"
		DOCKER_CREDENTIALS_ID="DOCKERHUB"
	}
	stages{
		stage('Clone Repository'){
			steps{
				git branch: 'main',
				url: ''
			}
		}

		stage('Install Dependencies & Build App'){
                        steps{
                                sh 'npm install',
				sh 'npm run build'
                        }
		}
	
		stage('Build Docker Image'){
                        steps{
                                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
                        }
                }

		stage('Docker Login'){
                        steps{
                                withCredentials([
					usernamePassword(
						credentialsId: DOCKER_CREDENTIALS_ID,
						usernameVariable: 'USERNAME',
						passwordVariable: 'PASSWORD'
					)
				]) {
					sh '''
					echo $PASSWORD | docker login -u $USERNAME --password-stdin
					'''
				}
                        }
                }

		stage('Push Image') {
			steps{
				sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
			}
		}
		
		stage('Deploy Kubernetes') {
			steps{
				sh '''
				kubectl set image deployment/simple-program1 \
				simple-program1=$IMAGE_NAME:$IMAGE_TAG
	
				kubectl rollout status deployment/simple-program1
				'''
			}
		}

	}
}
