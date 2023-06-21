pipeline {	
    agent {
        kubernetes{
	     yamlFile 'agent-devops.yaml'
	}
    }

    stages {

	stage('Git Source Stage') {
            steps {
                git clone 'https://github.com/shaked123456/Exercise-2023.git'
            }
        }
    
	stage('Build Stage') {    
            steps {
                sh 'docker build -t dotnetapp:v1 dotnet-app/samples/aspnetapp'
            }
         }

        stage('Upload Stage') {
            steps {
	        withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
                  sh 'docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}'
                  sh 'docker push shak2022/dotnetapp:${env.BUILD_NUMBER}'
                }
            }
        }

	stage('Deploy Stage'){
	    agent {
                kubernetes{
                     yamlFile 'agent-dotnetapp.yaml'
                }
            }

	    steps {
	        sh 'helm upgrade --set image.tag=${env.BUILD_NUMBER} dotnetapp dotnetapp -n dotnetapp'
	    }
	}	    
    }
    post {	
	always {
            cleanWs()
	}
	failure {
	    mail to: "shaked@wizards.co.il",
            subject: "Jenkins - Pipeline Failed",
            body: "Pipeline Failed: ${env.BUILD_URL}"
	}
	success {
	    mail to: "shaked@wizards.co.il",
            subject: "Jenkins - Pipeline Succeeded",
            body: "Pipeline Succeeded: ${env.BUILD_URL}"
	}	    
    }
}	
