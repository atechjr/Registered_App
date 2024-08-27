pipeline {
    agent { label 'DockerAgent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
	environment {
            APP_NAME = " Registered_App"
            RELEASE = "1.0.0"
            DOCKER_USER = "atechjr"
            DOCKER_PASS = 'dockerhub_jenkins_token'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
        stage("SonarQube Analysis"){
           steps {
	           script {
		            withSonarQubeEnv(credentialsId: 'sonarq-jenkins-token') { 
                    sh "mvn sonar:sonar"
		        }
	           }	
           }
       }
	    stage("Quality Gate"){
           steps {
               script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarq_jenkins_token '
                }	
            }
        }
	    stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
       }
    }
  }
