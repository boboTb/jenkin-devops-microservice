pipeline {
	agent any
	//agent {docker {image 'maven:3.8.6'}}
	environment {
		dockerHome = tool "MyDocker"
		mavenHome = tool "MyMaven"
		PATH = "$dockerHome/bin:$mavenHome/bin:$PATH"
	}
	stages {
		stage('CheckOut') {
			steps {
				sh 'mvn --version'
				echo "Build"
				echo 'docker version'
				echo "PATH - $PATH"
				echo "BUILD_NUMBER - $env.BUILD_NUMBER"
				echo "BUILD_ID - $env.BUILD_ID"
				echo "JOB_NAME - $env.JOB_NAME"
				echo "BUILD_TAG - $env.BUILD_TAG"
				echo "BUILD_URL - $env.BUILD_URL"
			}
		}
		stage('Compile') {
			steps {
				sh "mvn clean compile"
			}
		}
		stage('Test') {
			steps {
				sh "mvn test"
			}
		}
		stage('Integration Test') {
			steps {
				sh "mvn failsafe:integration-test failsafe:verify"
			}
		}
		stage('Package') {
			steps {
				sh "mvn package DskipTests"
			}
		}
		stage('Build Docker Image') {
			steps {
				script {
					dockerImage = docker.build("bobohubdocker/currency-exchange-devops:$env.BUILD_TAG")
				}
			}
		}
		stage('Push Docker Image') {
			steps {
				script {
					docker.withregistry("","dockerhub") {
					dockerImage.push()
					dockerImage.push('latest')
					}
				}
			}
		}
	}
}
