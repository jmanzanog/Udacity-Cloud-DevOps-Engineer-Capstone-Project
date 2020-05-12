pipeline {
	agent any
	stages {

		stage('Create kubernetes cluster') {
			steps {
				withAWS(region:'sa-east-1', credentials:'1da5412b-e88c-449a-8a60-1505a44f5865') {
					sh '''
						eksctl create cluster \
						--name capstonecluster \
						--version 1.15 \
						--nodegroup-name standard-workers \
						--node-type t2.micro \
						--nodes 2 \
						--nodes-min 1 \
						--nodes-max 3 \
						--node-ami auto \
						--region sa-east-1 \
						--zones sa-east-1a \
						--zones sa-east-1b \
						--zones sa-east-1c \
					'''
				}
			}
		}

		

		stage('Create conf file cluster') {
			steps {
				withAWS(region:'sa-east-1', credentials:'1da5412b-e88c-449a-8a60-1505a44f5865') {
					sh '''
						aws eks --region sa-east-1 update-kubeconfig --name capstonecluster
					'''
				}
			}
		}

	}
}
