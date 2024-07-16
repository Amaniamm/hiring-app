pipeline {
    agent any

    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Docker Build') {
            steps {
                sh "docker build . -t amanitechie/test:$BUILD_NUMBER"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}"
                    sh "docker push amanitechie/test:$BUILD_NUMBER"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'your-kubeconfig-credential-id', variable: 'KUBECONFIG')]) {
                    sh '''
                    kubectl --kubeconfig=$KUBECONFIG apply -f deployment.yaml
                    kubectl --kubeconfig=$KUBECONFIG set image deployment/my-app-deployment \
                        my-app-container=amanitechie/test:$BUILD_NUMBER --record
                    '''
                }
            }
        }
    }
}
