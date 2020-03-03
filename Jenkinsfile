pipeline {
    agent any
         environment {
                           PROJECT_ID = ‘piyush-devops’
                           CLUSTER_NAME = ‘kube-demo’
                           LOCATION = ‘us-central1-c’
                           CREDENTIALS_ID = ‘Kubernetesjenkins’

            }


    stages {
        stage (‘Scm checkout’ ) {
            steps {
               checkout scm
                     }
                }

        stage('Build') {
            steps {
                echo “Cleaning and packaging”
                sh ‘mvn clean package’

            }
        }

        stage('Test') {
            steps {
                echo “Testing..”
                sh ‘mvn test’
            }
        }

         stage(‘Build Docker Image’) {
            steps {
                script {
                  myimage = docker.build(“piyushdocker/devops:${env.BUILD_ID}”)
                }
            }
        }


         stage(‘Push Docker Image’) {
            steps {
                script {
                  docker.withRegistry(‘https://registry.hub.docker.com’, ‘docker’) {
                         myimage.push(“${env.BUILD_ID}”)
                  }
                }
            }
        }


        stage('Deploy to ‘K8) {
            steps {
                echo “Deployment Started..”
                 sh ‘ls -ltr’
                 sh ‘pwd’
                 sh “ sed -i ‘sed/tagversion/${env.BUILD_ID}/g’ deployment.yaml”
                 step([$class: ‘KubernetesEngineBuilder’, projectID: env.PROJECT_ID, clustername: env.CLUSTER_NAME, location: env.LOCATION])
                 echo “ Deployment Finished”
            }
        }
    }
}
