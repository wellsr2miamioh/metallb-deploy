pipeline {
    agent any 

    environment {
        GITHUB_URL = 'https://github.com/miamioh-cit/metallb-deploy.git'  // <---- Change this to match your cloned repository
        KUBECONFIG = credentials('roseaw-225')   // <---- Change this to match your kubernetes cluster credentials
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
                    sh "kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.3/config/manifests/metallb-native.yaml"
                    sh "kubectl apply -f l2advertisement.yaml -n metallb-system"
                    sh "kubectl apply -f ipaddresses.yaml -n metallb-system"
                    sh "kubectl get IPAddressPool -A"
                    sh "kubectl get L2Advertisement -A"
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
