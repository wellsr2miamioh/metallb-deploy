pipeline {
    agent any 

    environment {
        GITHUB_URL = 'https://github.com/miamioh-cit/metallb.git'
        KUBECONFIG = credentials('roseaw')
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']],
                          userRemoteConfigs: [[url: "${GITHUB_URL}"]]])
            }
        }
        stage('Deploy to Kubernetes Cluster') {
            steps {
                script {
                    sh "kubectl create namespace metallb-system"
                    sh "kubectl apply -f l2advertisements.yaml -n metallb-system"
                    sh "kubectl apply -f ipaddresses.yaml -n metallb-system"
                }
            }
        }
    }

    post {
        success {
            slackSend color: "good", message: "Build Completed: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        }
        failure {
            slackSend color: "danger", message: "Build Completed: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        }
    }
}
