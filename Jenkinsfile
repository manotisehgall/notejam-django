pipeline{
    environment{
        dockerimagename = "guestdev/django-notejam-kube"
        dockerImage = ""
        // registrycredentials = 'dockerhub_id'
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

            // stage('K8s Deployment'){
            //     steps{
            //         script{
            //         // kubernetesDeploy(configs: 'postgres-secret.yaml' , kubeconfigId: 'kubeconfig')
            //         kubernetesDeploy(configs: 'postgres-deployment.yaml' )
            //         // kubernetesDeploy(configs: 'postgres-service.yaml' , kubeconfigId: 'kubeconfig')
            //         // kubernetesDeploy(configs: 'deployment.yaml' , kubeconfigId: 'kubeconfig')
            //         // kubernetesDeploy(configs: 'pv-def.yaml' , kubeconfigId: 'kubeconfig')
            //         // kubernetesDeploy(configs: 'pvc-def.yaml' , kubeconfigId: 'kubeconfig')
            //         // kubernetesDeploy(configs: 'notejam-ingress.yaml' , kubeconfigId: 'kubeconfig')
            //         }
            //     }
            // }
            
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



