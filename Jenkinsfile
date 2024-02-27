pipeline {
    agent any 

    environment {
        GITHUB_URL = 'https://github.com/miamioh-cit/prometheus.git'
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
                    sh "kubectl create namespace -n prometheus"
                    sh "kubectl cp prometheus-config.yaml prometheus-config.yaml -n prometheus"
                    sh "kubectl cp prometheus-deployment.yaml prometheus-deployment.yaml -n prometheus"
                    sh "kubectl cp prometheus-service.yaml prometheus-service.yaml -n prometheus"
                    sh "kubectl apply -f prometheus-config.yaml -n prometheus"
                    sh "kubectl apply -f prometheus-deployment.yaml -n prometheus"
                    sh "kubectl apply -f prometheus-service.yaml -n prometheus"
                }
            }
        }
    }

    post {
        always {
            slackSend color: "good", message: "Build Completed: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        }
    }
}
