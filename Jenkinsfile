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
                    def namespaceExists = sh (script: "kubectl get namespace prometheus --ignore-not-found", returnStatus: true) == 0
                    if (namespaceExists) {
                        echo "Namespace Exists"
                    } else {
                        sh "kubectl create namespace prometheus"
                    }
                    def podName = sh(script: "kubectl get pods -l app=prometheus -n prometheus -o jsonpath='{.items[0].metadata.name}'", returnStdout: true).trim()
                    if (podName) {
                        echo "Found pod: ${podName}"
                    }
                    sh "kubectl cp ./prometheus-config.yaml prometheus/${podName}:/prometheus-config.yaml"
                    sh "kubectl cp ./prometheus-deployment.yaml prometheus/${podName}:/prometheus-deployment.yaml"
                    sh "kubectl cp ./prometheus-service.yaml prometheus/${podName}:/prometheus-service.yaml -n prometheus"
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
