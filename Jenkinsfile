pipeline {
    agent any
    
    tools {
        maven 'M3' // we will configure this in Jenkins
    }

    environment {
        REGISTRY = 'docker.io/letianwilliamma'
        IMAGE = 'springboot-crud'
        TAG = "${env.BUILD_NUMBER}"
        OPENSHIFT_PROJECT = 'williammaletian-dev'
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
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $REGISTRY/$IMAGE:$TAG ."
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub-password', variable: 'DOCKER_PASSWORD')]) {
                    sh """
                        echo $DOCKER_PASSWORD | docker login -u $REGISTRY --password-stdin
                        docker push $REGISTRY/$IMAGE:$TAG
                    """
                }
            }
        }
        stage('Deploy to OpenShift') {
            steps {
                withCredentials([string(credentialsId: 'openshift-token', variable: 'OPENSHIFT_TOKEN')]) {
                    sh """
                        oc login --token=$OPENSHIFT_TOKEN --server=https://api.openshift.example.com:6443
                        oc project $OPENSHIFT_PROJECT
                        oc set image deployment/$IMAGE $IMAGE=$REGISTRY/$IMAGE:$TAG --record
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
