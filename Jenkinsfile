pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_HUB_TOKEN = credentials('githubsecret')
    }

    stages {
        stage('Docker Build') {
            steps {
                sh "docker build . -t maksoft121/hiring-app:${IMAGE_TAG}"
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'githubsecret', variable: 'githubsecret')]) {
                        sh '''
                        echo $DOCKER_HUB_TOKEN | docker login -u maksoft121 --password-stdin
                        docker push maksoft121/hiring-app:${IMAGE_TAG}
                        '''
                    }
                }
            }
        }
        stage('Checkout K8S manifest SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/betawins/Hiring-app-argocd.git'
            }
        }
        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'Github_server', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat dev/deployment.yaml
                        sed -i "s/5/${IMAGE_TAG}/g" dev/deployment.yaml
                        cat dev/deployment.yaml
                        git add dev/deployment.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/maksoft121/Hiring-app-argocd.git main
                        '''
                    }
                }
            }
        }
    }
}
