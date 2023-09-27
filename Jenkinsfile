pipeline{
    environment{
        dockerimagename = "guestdev/django-notejam1"
        dockerImage = ""
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        KUBECONFIG = credentials('kubeconfig')
    }
    agent any 
        stages{
            stage('clone'){
                steps{
                    git branch: 'master', credentialsId: 'github', url: 'https://github.com/manotisehgall/notejam-django.git'

                }
            }
        
            stage('Build Image'){
                steps{ 
                    script{
                        dockerImage = docker.build dockerimagename
                    }

                }
            }
            stage("Dockerhub login"){
                steps{
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                }
            }
            
            stage('Pushing image'){
                steps{
                    script{
                        dockerImage.tag("latest")
                        // docker.withRegistry(' https://hub.docker.com/ ', registrycredentials){
                            dockerImage.push("latest")
                       }
                    }
                }

            
            stage('Deployment'){
                steps{
                    script{
                    sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/notejam-jenkins/postgres-secret.yaml"
                    sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/notejam-jenkins/postgres-deployment.yaml"
                    sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/notejam-jenkins/postgres-service.yaml"
                    sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/notejam-jenkins/deployment.yaml"
                    sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/notejam-jenkins/pv-def.yaml"
                    sh "kubectl --kubeconfig $KUBECONFIG apply -f /var/lib/jenkins/workspace/notejam-jenkins/pvc-def.yaml"
                }
            }
    
        }
        }    
    }



