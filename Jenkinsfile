pipeline {
    agent {
        label 'jenkins-slave'
    }
    environment {
        GCP_PROJECT_ID= "onblick-dev"
        APP_NAME= "aditya-dev-app"
        GCP_ZONE = "us-central1-a"
        GKE_CLUSTER_NAME = "dev-onblick-apps-us-ct1-gke"
        GIT_CREDS = ""
        GIT_TOKEN = ""
    }
    stages {
        stage ('Main Stage') {
            steps { 
                script {
                    stage("Checkout code") {
                        script {
                            sh 'git checkout'
                        }
                    }
                    stage("Build image") {
                        script {
                            sh '''
                            docker build . -t gcr.io/${GCP_PROJECT_ID}/${APP_NAME}:${GIT_COMMIT}
                            '''
                        }
                    }
                    
                }
            }
        }
    }
}
