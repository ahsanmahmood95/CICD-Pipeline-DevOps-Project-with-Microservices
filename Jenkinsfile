pipeline {
    agent any

    stages {
        stage('Deploy To Kubernetes') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'ecommerce-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://CE323DB9F81B553BE0FF6A72A17A88EC.gr7.ap-south-1.eks.amazonaws.com']]) {
    // some blocks
                     sh "kubectl apply -f deployment-service.yml"
                    
                }
            }
        }
        
                stage('verify Deployment') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'ecommerce-1', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://CE323DB9F81B553BE0FF6A72A17A88EC.gr7.ap-south-1.eks.amazonaws.com']]) {
                    sh "kubectl get svc -n webapps"
                }
            }
        }
    }
}
