pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('Dockerhub')
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
                    cd Dockerfile/
                    make base
                    cd nf_nrf/
                    sudo docker images -a
                    sudo docker build -t gradproj/5G-NRF:latest . 
                    sudo docker images -a
                """)
                    }
                    }
        
        stage('Pushing to Dockerhub') {
                steps {
                    sh 'docker push gradproj/5G-NRF:latest'
                }
                }

        stage('Build and Package Helm Chart') {
            steps {
                sh 'helm package ./helm'
            }
        }
        stage('Configure Kubernetes Context') {
            steps {
                sh 'aws eks --region $AWS_REGION update-kubeconfig --name <cluster-name>'
            }
        }
        stage('Deploy Helm Chart') {
            steps {
                sh 'helm upgrade --install nrf ./helm/'
            }
        }
        stage('Deploy to EKS') {
                steps {
                    sh 'docker push gradproj/5G-NRF:latest'
                }
                }
            }
        }