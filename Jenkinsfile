pipeline{
    environment{
        dockerimagename = "guestdev/django-notejam-kube"
        dockerImage = ""
        registrycredentials = 'dockerhub_id'
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
            
            stage('Pushing image'){
                steps{
                    script{
                        dockerImage.tag("latest")
                        docker.withRegistry(' https://hub.docker.com/ ', registrycredentials){
                            dockerImage.push("latest")
                       }
                    }
                }
            }
            stage('Deployment'){
                steps{
                    scripts{
                        kubernetesDeploy(configs: 'postgres-secret','postgres-deployment.yaml','postgres-service.yaml', 'deployment.yaml','pv-def.yaml', 'pvc-def.yaml','notejam-ingress.yaml')
                }
                }
            }
        }
    }



