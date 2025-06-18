pipeline {
    agent any
    
    tools {
        maven 'M3' // Make sure this is configured in Jenkins
    }

    environment {
        OPENSHIFT_PROJECT = 'williammaletian-dev' // Replace with your OpenShift project/namespace
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[url: 'https://github.com/WilliamMLT/ntucfulljenkins.git']]
                ])
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Deploy to OpenShift') {
            steps {
                withCredentials([string(credentialsId: 'openshift-token', variable: 'OPENSHIFT_TOKEN')]) {
                    sh """
                        oc login --token=$OPENSHIFT_TOKEN --server=https://api.openshift.example.com:6443
                        oc project $OPENSHIFT_PROJECT
                        # Add your OpenShift deployment commands here (e.g., oc apply, oc rollout, etc.)
                    """
                }
            }
        }
        stage('Test in OpenShift') {
            steps {
                // Replace with your OpenShift route
                sh "curl http://your-openshift-route/api/health || true"
            }
        }
    }
}
