pipeline {
    agent { label 'DockerAgent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }

    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'main', credentialsId: 'github', url: 'https://github.com/atechjr/Registered_App'
                }
        }

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }

       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
    }
        stage ("SonarQube Analysis"){
                steps {
	           script {
		        withSonarQubeEnv(credentialsId: 'jenkins2sonar'){ 
                        sh "mvn sonar:sonar"
		        }
	           }	
           }
       }
}
