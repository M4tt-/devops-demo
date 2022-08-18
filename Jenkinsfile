pipeline {

    agent any
	//agent { docker { image "maven:3.6.3" } }

	environment {

		dockerHome = tool "myDocker"
		mavenHome = tool "myMaven" 
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"

	}

	stages {
		stage('Checkout'){
			steps {
				sh "mvn --version"
				sh "docker version"
				echo "Checkout"
				echo "PATH - $env.PATH"
				echo "BUILD_NUMBER - $env.BUILD_NUMBER"
				echo "BUILD_ID - $env.BUILD_ID"
				echo "JOB_NAME - $env.JOB_NAME"
				echo "BUILD_TAG - $env.BUILD_TAG"
			}
		}
		stage('Compile'){
			steps {
				sh "mvn clean compile"
			}
		}
		stage('Test'){
			steps {
				sh "mvn test"
			}
		}
		stage('Integration Test'){
			steps {
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}
		stage('Package'){
			steps {
				sh "mvn package -D skipTests"
			}
		}
		stage('Build Docker Image'){
			steps {
				//"docker build -t m4ttl33t/currency-exchange-devops:$env.BUILD_TAG"
				script {
					dockerImage = docker.build("m4ttl33t/currency-exchange-devops:${env.BUILD_TAG}")
				}
			}
		}
		stage('Push Docker Image'){
			steps {
				script {
					docker.withRegistry('', 'dockerhub'){
						dockerImage.push();
						dockerImage.push('latest');
					}
				}
			}
		}
	} 
	post {
		always {
			echo "I'm awesome. I run always."
		}
		success {
			echo "I run when you are successful."
		}
		failure {
			echo "I run when you fail."
		}
	}

    // changed - when build-to-build status changes, e.g., fail-to-success

}
