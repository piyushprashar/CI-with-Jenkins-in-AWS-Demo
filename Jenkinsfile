pipeline {
    agent any
         environment {
                           PROJECT_ID = 'piyush-devops'
                           CLUSTER_NAME = 'kube-demo'
                           LOCATION = 'us-central1-c'
                           CREDENTIALS_ID = 'Kubernetesjenkins'

            }


    stages {
        stage ('Scm checkout' ) {
            steps {
               checkout scm
                     }
                }

        stage('Build') {
            steps {
                echo "Cleaning and packaging"
                sh 'mvn clean package'

            }
        }

        stage('Test') {
            steps {
                echo "Testing.."
                sh 'mvn test'
            }
        }

         stage('Build Docker Image') {
            steps {
                script {
                  myimage = docker.build("gcr.io/piyush-devops/piyush348/piyushdocker:${env.BUILD_ID}")
                }
            }
        }


         stage('Push Docker Image') {
            steps {
                script {
                  docker.withRegistry('https://gcr.io', 'gcr:piyush-devops') {
                         myimage.push("${env.BUILD_ID}")
                  }
                }
            }
        }


        stage('Deploy to K8') {
            steps {
                echo "Deployment Started.."
                 sh 'ls -ltr'
                 sh 'pwd'
                 sh "sed -i 's/tagversion/${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true]) 
                echo "Deployment Finished"
            }
        }
    }
}
