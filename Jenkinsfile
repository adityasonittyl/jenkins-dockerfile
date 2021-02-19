pipeline {
    agent {
        label 'jenkins-slave'
    }
    environment {
        GCP_PROJECT_ID= "onblick-dev"
        APP_NAME= "aditya-dev-app"
        GCP_ZONE = "us-central1-a"
        GKE_CLUSTER_NAME = "dev-onblick-apps-us-ct1-gk"

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
                    stage("Push image to GCR") {                                              
                        script {
                            withDockerRegistry([credentialsId: "gcr:${GCP_PROJECT_ID}", url: "https://gcr.io"]) {              
                            sh "docker push gcr.io/${GCP_PROJECT_ID}/${APP_NAME}:${GIT_COMMIT}"
                            }
                        
                        }
                    }
                    stage('Update yamls and create PR') {
                            script {
                                withCredentials([file(credentialsId: 'github-token-onblick', variable: 'GITHUB_TOK')]){
                                    sshagent (credentials: ['argocd-ssh-key']) {
                                        dir('dev'){
                                        sh '''
                                        git clone git@github.com:theadisoni/jenkins-argocd.git
                                        cd jenkins-argocd
                                        ls -lart
                                        git checkout -b pr-branch-${GIT_COMMIT}
                                        envsubst < sample.yaml.tpl > sample.yaml
                                        cat sample.yaml
                                        echo $GIT_COMMIT                                  
                                        gh auth login --with-token < $GITHUB_TOK
                                        git push --set-upstream origin pr-branch-${GIT_COMMIT}
                                        git add .
                                        git commit -m "${GIT_COMMIT}" 
                                        git push origin pr-branch-${GIT_COMMIT}  
                                        gh pr create --title "The bug is fixed" --body "Everything works again" --head pr-branch-${GIT_COMMIT}
                                        '''
                                        }
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
post {
        always {
            deleteDir() /* clean up our workspace */
        }
    }    
}
