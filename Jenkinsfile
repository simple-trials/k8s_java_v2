pipeline {
  agent {
      label 'docker'
    }
    environment {
		LOGIN_CREDENTIALS=credentials('docker-hub')
	}
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/krishnalakamsani/java-application.git'
        }
    }
    stage('Build') {
      steps {
        sh 'mvn clean package'
        }
    }
    stage('Build and Push Docker Image') {
      steps {
        sh 'docker build -t krishnalakamsani/java-application:$BUILD_NUMBER .'
        sh 'echo $LOGIN_CREDENTIALS_PSW | docker login -u $LOGIN_CREDENTIALS_USR --password-stdin'
        sh 'docker push krishnalakamsani/java-application:$BUILD_NUMBER'
        }
    }
    stage('k8s deployment') {
        agent {
        label 'k8s'
        }
        steps {
         git branch: 'main', url: 'https://github.com/krishnalakamsani/java-application.git'
         sh 'kubectl apply -f deployment.yml'
         sh 'kubectl apply -f service.yml'
        }
    }
  }
}
