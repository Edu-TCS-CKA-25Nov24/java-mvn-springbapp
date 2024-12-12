
pipeline {
    
    agent { label 'slave1' }


	environment {	
		DOCKERHUB_CREDENTIALS=credentials('dockerloginid')
	}
	
    stages {
        stage('SCM Checkout') {
            steps {
                echo 'Perform SCM Checkout'
				git 'https://github.com/Edu-TCS-CKA-25Nov24/java-mvn-springbapp.git'
            }
        }
        stage('Application Build') {
            steps {
                echo 'Application Build'
                sh 'mvn clean package'
                
            }
        }
        stage('Build Docker Image') {
            steps {
				sh "docker build -t loksaieta/sk-wd-webapp:${BUILD_NUMBER} ."
				sh 'docker image list'
				sh "docker tag loksaieta/sk-wd-webapp:${BUILD_NUMBER} loksaieta/sk-wd-webapp:latest"
            }
        }
		stage('Login2DockerHub') {

			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}		
		stage('Publish_to_Docker_Registry') {
			steps {
				sh "docker push loksaieta/sk-wd-webapp:latest"
			}
		}		
        stage('Deploy to Kubernetest Cluster') {
            steps {
                script{
                    sshPublisher(publishers: [sshPublisherDesc(configName: 'Kube_Master', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'kubectl apply -f kubedeploy.yaml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '.', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.yaml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
                }
            }
        }
    }
}
