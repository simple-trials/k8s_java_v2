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
    stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "Jenkins-Zero-To-Hero"
            GIT_USER_NAME = "iam-veeramalla"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "abhishek.xyz@gmail.com"
                    git config user.name "Abhishek Veeramalla"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
                    git add java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
            }
        }
    }
  }
}