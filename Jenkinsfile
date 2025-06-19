pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git credentialsId: 'github-token',
                    url: 'https://github.com/akylgit/jenkins-argo.git',
                    branch: 'main'
            }
        }

        stage('Build Docker') {
            steps {
                script {
                    sh '''
                    echo 'Build Docker Image'
                    docker build -t akyldocker25/jenkins-argo:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts') {
            steps {
                script {
                    sh '''
                    echo 'Push to Repo'
                    docker push akyldocker25/jenkins-argo:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Checkout K8S manifest SCM') {
            steps {
                git credentialsId: 'github-token',
                    url: 'https://github.com/akylgit/jenkins-argo.git',
                    branch: 'main'
            }
        }

        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                        sh '''
                        sed -i "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        git config user.email "jenkins@example.com"
                        git config user.name "Jenkins CI"
                        git add deploy.yaml
                        git commit -m "Updated the deploy yaml | Jenkins Pipeline"
                        git push https://${GITHUB_TOKEN}@github.com/akylgit/jenkins-argo.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
