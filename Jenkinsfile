pipeline {
	agent any
	// agent { docker { image  "maven:3.6.3" } }
	environment {
		mavenHome = tool "myMaven"
		dockerHome = tool "myDocker"
		PATH = "$mavenHome/bin:$dockerHome/bin:$PATH"
	}
	stages {
		stage("Checkout") {
			steps {
				sh "mvn --version"
				sh "docker version"
				echo "Build"
				echo "PATH: $PATH"
				echo "BUILD_NUMBER: $env.BUILD_NUMBER"
				echo "BUILD_ID: $env.BUILD_ID"
				echo "JOB_NAME: $env.JOB_NAME"
				echo "BUILD_TAG: $env.BUILD_TAG"
				echo "BUILD_URL: $env.BUILD_URL"
			}
		}
		stage("Compile") {
			steps {
				echo "mvn clean compile"
				sh "mvn clean compile"
			}
		}
		stage("Test") {
			steps {
				echo "mvn test"
				sh "mvn test"
			}
		}
		stage("Integration Test") {
			steps {
				// Integration Test in Maven uses "maven-failsafe-plugin"
				echo "mvn failsafe:integration-test failsafe:verify"
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}
		stage("Package") {
			steps {
				// create jar file
				echo "mvn package -DskipTests"
				sh "mvn package -DskipTests"
			}
		}
		stage("Build Docker Image") {
			steps {
				// sh "docker build -t chris345000/currency-exchange-devops:$env.BUILD_TAG"
				script {
					dockerImage = docker.build("chris345000/currency-exchange-devops:${env.BUILD_TAG}")
				}
			}
		}
		stage("Push Docker Image") {
			steps {
				script {
					docker.withRegistry("", "dockerhub") {
						dockerImage.push();
						dockerImage.push('latest');
					}
				}
			}
		}
	}

	post {
		success {
			echo "Success."
		}
		failure {
			echo "Failed."
		}
	}
}	
