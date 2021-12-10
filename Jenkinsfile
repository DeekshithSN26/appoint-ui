pipeline{
    agent any 
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
       
        stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                             sh '''
                                docker build -t deekshithsn/userui:${VERSION} .
                                docker login -u admin -p $docker_password
                                docker push  deekshithsn/userui:${VERSION}
                                docker rmi deekshithsn/userui:${VERSION}
                            '''
                    }
                }
            }
        }

        stage('Deploying application on k8s cluster') {
            steps {
               script{
                   withCredentials([kubeconfigFile(credentialsId: 'kubernetes-config', variable: 'KUBECONFIG')]) {
                        
                          sh '''
                          sed -i 's,IMAGENAME,deekshithsn/userui:${VERSION},g' userui.yaml
                          kubectl apply -f userui.yaml
                          ''' 
                        
                    }
               }
            }
        }

    }
}
