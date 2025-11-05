pipeline {
    agent none
    stages {
	stage('Checkout') {
		agent any
		steps {
			git branch: 'main',
			url: 'https://github.com/ysk6506/git-test.git'
		}
	}
	stage('Build') {
		agent {
			docker { image 'maven:3-openjdk-17' }
		}
		steps {
			sh 'mvn clean package'
		}
	}
	stage('Image Build'){
		agent any
		steps {
			sh 'docker image build -t tomcat:hello .'
		}
	}
	stage('Image Tag'){
		agent any
		steps {
			sh 'docker image tag tomcat:hello ysk/tomcat:v1'
			sh 'docker image tag tomcat:hello ysk/tomcat:latest'
		}
	}
	stage('Image Push'){
		agent any
		steps {
			withDockerRegistry(credentialsId: 'docker-hub-token', url: 'https://index.docker.io/v1/') {
				sh 'docker image push ysk/tomcat:$BUILDNUMBER'
				sh 'docker image push ysk/tomcat:latest'
			}
		}
	}
	stage('Running Container') {
		agent {
			docker { image 'docker:dind' }
		}
		steps {
			sh 'docker -H tcp://192.168.56.103:2375 run -d --name webserver -p 80:8080 ysk/tomcat:latest'
		}
	}
    }
}
