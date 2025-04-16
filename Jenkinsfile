pipeline {
    agent { label 'azure-agent' }

    environment {
        KUBECONFIG_CREDENTIALS = 'kubernetes-credentials'
    }

    stages {
        stage('Checkout Deployment Files') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[
                        credentialsId: '30989c36-de19-497a-b96e-17aa4b90c235',
                        url: 'https://github.com/Project-TFE/Kubernetes-Cluster.git'
                    ]]
                )
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: env.KUBECONFIG_CREDENTIALS]) {
                        sh """
                            kubectl apply -f Docker/k8s/namespace.yaml
                            kubectl apply -f Docker/k8s/database-deployment.yaml
                            kubectl apply -f Docker/k8s/backend-deployment.yaml
                            kubectl apply -f Docker/k8s/frontend-deployment.yaml
                        """
                    }
                }
            }
        }
    }
}
