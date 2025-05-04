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

        stage('Start Kubernetes Cluster') {
            steps {
                script {
                    sh '''
                        echo "[INFO] Vérification du statut de Minikube..."
                        if ! minikube status | grep -q "host: Running"; then
                            echo "[INFO] Minikube n'est pas actif. Démarrage en cours..."
                            minikube start
                        else
                            echo "[INFO] Minikube est déjà actif."
                        fi
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withKubeConfig([credentialsId: env.KUBECONFIG_CREDENTIALS]) {
                        sh '''
                            echo "[INFO] Déploiement des ressources Kubernetes..."

                            kubectl apply -f Docker/k8s/namespace.yaml
                            kubectl apply -f Docker/k8s/database-deployment.yaml
                            kubectl apply -f Docker/k8s/backend-deployment.yaml
                            kubectl apply -f Docker/k8s/frontend-deployment.yaml
                            kubectl apply -f Docker/k8s/grafana-deployment.yaml
                            kubectl apply -f Docker/k8s/prometheus-config.yaml
                            kubectl apply -f Docker/k8s/prometheus-deployment.yaml
                            kubectl apply -f Docker/k8s/node-exporter.yaml
                            kubectl apply -f Docker/k8s/service-monitoring.yaml
                            kubectl apply -f Docker/k8s/cadvisor.yaml

                            echo "[INFO] Vérification du rollout..."
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
