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
                        sh '''
                            # Vérifie si Minikube est actif
                            if ! minikube status | grep -q "host: Running"; then
                                echo "Minikube n'est pas actif. Démarrage en cours..."
                                minikube start
                            else
                                echo "Minikube est déjà actif."
                            fi

                            # Déploiement Kubernetes
                            kubectl apply -f Docker/k8s/namespace.yaml
                            kubectl apply -f Docker/k8s/database-deployment.yaml
                            kubectl apply -f Docker/k8s/backend-deployment.yaml
                            kubectl apply -f Docker/k8s/frontend-deployment.yaml
                            kubectl apply -f Docker/k8s/prometheus-config.yaml
                            kubectl apply -f Docker/k8s/prometheus-deployment.yaml

                            # Suivi du rollout pour chaque déploiement dans le namespace myapp
                            kubectl rollout status deployment/database -n myapp
                            kubectl rollout status deployment/backend -n myapp
                            kubectl rollout status deployment/frontend -n myapp
                        '''
                    }
                }
            }
        }
    }
}
