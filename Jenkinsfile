pipeline {

    agent any
	//agent { docker { image "maven:3.6.3" } }

	triggers {
		GenericTrigger(
			genericVariables: [
				[key: 'email', value: '$.actor.emailAddress'],
				[key: 'event', value: '$.eventKey'],
				[key: 'ssh_link', value: '$.pullRequest.toRef.repository.links.clone[0].href'],
			],

		causeString: 'Triggered on ${event}',
		token: '11b8c3b49dbe7c8a5ff593ada2a52f17ef',
		tokenCredentialId: 'mrunyon',
		printPostContent: true,
		silentResponse: false,

		)
	}

	environment {

		ADMIN_EMAIL = "admin@company.com"
		dockerHome = tool "myDocker"
		mavenHome = tool "myMaven" 
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
		CHECKOUT_PATH = "~ci/repos/my_repo"
		CREDENTIALS = "my_scm_credentials"
		LINT_SCRIPT_PATH = "/path/to/linter/"
		TEST_SCRIPT_PATH = "/usr/bin/python3/"
	}

	stages {
		stage('Checkout'){
			steps {
				sh "docker version"
				echo "Checkout"
				echo "PATH - $env.PATH"
				echo "BUILD_NUMBER - $env.BUILD_NUMBER"
				echo "BUILD_ID - $env.BUILD_ID"
				echo "JOB_NAME - $env.JOB_NAME"
				echo "BUILD_TAG - $env.BUILD_TAG"

				checkout(
						[$class: 'GitSCM',
						branches: [[name: '*/master']],
						extensions: [[
										$class: 'RelativeTargetDirectory',
										relativeTargetDir: WORKSPACE
									],
									[
									$class: 'SubmoduleOption', 
									disableSubmodules: false, 
									parentCredentials: true, 
									recursiveSubmodules: true, 
									reference: '', 
									trackingSubmodules: false
									]],
						userRemoteConfigs: [[credentialsId: CREDENTIALS,
											url: ${ssh_link}]]
											]
				)
			}
		}
		stage('Test'){
			steps {
				sh "${TEST_SCRIPT_PATH}python -m pytest ${WORKSPACE}\\python\\tests\\ "
			}
		}
		stage('Lint'){
			steps {
				sh "${LINT_SCRIPT_PATH} | ${WORKSPACE}\\lint_results.txt"
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
			mail bcc: ADMIN_EMAIL,
			body: "The build ${env.BUILD_ID} was successful. "
			from: 'Your friendly neighborhood Spiderman',
			replyTo: '',
			subject: "SUCCESS: ${env.BUILD_TAG}",
			to: email 
		}
		failure {
			mail bcc: ADMIN_EMAIL,
			body: "The build ${env.BUILD_ID} was not successful. "
			from: 'Your friendly neighborhood Spiderman',
			replyTo: '',
			subject: "FAILURE: ${env.BUILD_TAG}",
			to: email 
		}
	}
    // changed - when build-to-build status changes, e.g., fail-to-success
}
