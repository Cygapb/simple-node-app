pipeline {
    agent any
    environment {
        IMAGE_NAME = 'cygarpb/train-schedule-simple'
        PROJECT_ID = 'dynamic-pivot-282919'
        CLUSTER_NAME = 'k8s'
        LOCATION = 'us-central1'
        CREDENTIALS_ID = 'k8s'
    }
    stages {
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("IMAGE_NAME:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}
