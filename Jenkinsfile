pipeline {
	agent any
	stages {

		stage('Lint HTML') {
			steps {
				sh 'tidy -q -e *.html'
			}
		}
		
		stage('Build Docker Image') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						docker build -t jmanzanog/capstone .
					'''
				}
			}
		}

		stage('Push Image To Dockerhub') {
			steps {
				withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]){
					sh '''
						docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
						docker push jmanzanog/capstone
					'''
				}
			}
		}

		stage('Set current
		 context') {
			steps {
				withAWS(region:'sa-east-1', credentials:'1da5412b-e88c-449a-8a60-1505a44f5865') {
					sh '''
						kubectl config use-context arn:aws:eks:sa-east-1:239696499026:cluster/capstonecluster
					'''
				}
			}
		}

		stage('Deploy blue container') {
			steps {
				withAWS(region:'sa-east-1', credentials:'1da5412b-e88c-449a-8a60-1505a44f5865') {
					sh '''
						kubectl apply -f ./blue-controller.json
					'''
				}
			}
		}

		stage('Deploy green container') {
			steps {
				withAWS(region:'sa-east-1', credentials:'1da5412b-e88c-449a-8a60-1505a44f5865') {
					sh '''
						kubectl apply -f ./green-controller.json
					'''
				}
			}
		}

		stage('Create the service in the cluster, redirect to blue') {
			steps {
				withAWS(region:'sa-east-1', credentials:'1da5412b-e88c-449a-8a60-1505a44f5865') {
					sh '''
						kubectl apply -f ./blue-service.json
					'''
				}
			}
		}

		stage('Wait user approve') {
            steps {
                input "Ready to redirect traffic to green?"
            }
        }

		stage('Create the service in the cluster, redirect to green') {
			steps {
				withAWS(region:'sa-east-1', credentials:'1da5412b-e88c-449a-8a60-1505a44f5865') {
					sh '''
						kubectl apply -f ./green-service.json
					'''
				}
			}
		}

	}
}
