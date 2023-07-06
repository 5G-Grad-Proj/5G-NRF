pipeline {
    agent any
    triggers {
    githubPush()
  }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('Dockerhub')
        // AWS_ACCESS_KEY_ID     = credentials('AWS').AWS_ACCESS_KEY_ID
        // AWS_SECRET_ACCESS_KEY = credentials('AWS').AWS_SECRET_ACCESS_KEY
        AWS_DEFAULT_REGION    = 'us-east-1'
        }

    stages {
        stage('Verify Branch') {
            steps {
                echo "$GIT_BRANCH"
            }
        }

        stage('Login to Dockerhub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('docker build') {
            steps {
                sh(script: """
                    docker images -a
                    docker build -t gradproj/5g-nrf:latest . 
                    docker images -a
                """)
                    }
                    }

        stage('Pushing to Dockerhub') {
            steps {
                sh 'docker push gradproj/5g-nrf:latest'
            }
        }

        stage('Build and Package Helm Chart') {
            steps {
                sh 'helm package ./helm/'
            }
        }

        stage('Configure Kubernetes Context') {
            steps {
                sh 'aws eks --region us-east-1 update-kubeconfig --name 9G-Core-Net'
                sh 'helm upgrade --install nrf ./helm/'
            }
        }

        // stage('Deploy Helm Chart') {
        //     steps {
        //         sh 'helm upgrade --install nrf ./helm/'
        //     }
        // }

        stage('Deploy to EKS') {
                steps {
                    sh 'docker push gradproj/5g-nrf:latest'
                }
                }
            }
        }