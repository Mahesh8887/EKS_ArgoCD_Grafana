pipeline {
    agent any
    stages{
        stage("Clone Code"){
            steps{
                git url: "https://github.com/Mahesh8887/EKS_ArgoCd_Grafana.git", branch: "main"
            }
        }
        stage("Build and Test"){
            steps{
                sh "docker build . -t node-todo-app"
            }
        }
        stage('Static Code Analysis') {
      environment {
        SONAR_URL = "http://54.179.72.198:9000"
      }
      steps {
        withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
        }
      }
    }   
      stage("Push to Docker Hub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"docker",passwordVariable:"dockerHubPass",usernameVariable:"dockerUser")]){
                sh "docker tag node-todo-app ${env.dockerUser}/node-todo-app:latest"
                sh "docker login -u ${env.dockerUser} -p ${env.dockerHubPass}"
                sh "docker push ${env.dockerUser}/node-todo-app:latest"
                }
            }
        }
        stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "Jenkins-Zero-To-Hero"
            GIT_USER_NAME = "writetoritika"
        }
        steps {
            withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                sh '''
                    git config user.email "maheshbiradar88878@gmail.com"
                    git config user.name "mahesh8887"
                    BUILD_NUMBER=${BUILD_NUMBER}
                    sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" web-app-manifests/deployment.yml
                    git add web-app-manifests/deployment.yml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                '''
            }
        }
    }
        
    }
}
