pipeline {
    agent any
    stages {
        stage('Build and Test') {
            steps {
                sh '''
                    sudo apt-get update
                    sudo apt-get install -y docker.io
                '''
                sh 'docker build -t dada-dev/testing-client -f ./client/Dockerfile.dev ./client'
                sh 'docker run dada-dev/testing-client npm test -- --coverage'
            }
        }
        stage('Push Docker Images') {
            steps {
                sh 'docker build -t dada-dev/multi-dc ./client'
                sh 'docker build -t dada-dev/multi-dn ./nginx'
                sh 'docker build -t dada-dev/multi-ds ./server'
                sh 'docker build -t dada-dev/multi-dw ./worker'

                /* groovylint-disable-next-line LineLength */
                withCredentials([usernamePassword(credentialsId: 'Dada-Docker-Credentials', passwordVariable: 'DOCKER-PASSWORD', usernameVariable: 'DOCKER-ID')]) {
                    sh 'echo "$DOCKER-PASSWORD" | docker login -u "$DOCKER-ID" --password-stdin'
                }

                // Provided by CHATGPT
                // withCredentials([string(credentialsId: 'docker-password', variable: 'DOCKER-PASSWORD')]) {
                //     sh 'echo "$DOCKER-PASSWORD" | docker login -u "$DOCKER-ID" --password-stdin'
                // }

                sh 'docker push dada-dev/multi-dc'
                sh 'docker push dada-dev/multi-dn'
                sh 'docker push dada-dev/multi-ds'
                sh 'docker push dada-dev/multi-dw'
            }
        }
        stage('Deploy to Elastic Beanstalk') {
            steps {
                // Provided by CHATGPT
                // withCredentials([string(credentialsId: 'aws-access-key-id', variable: 'AWS-ACCESS-KEY-ID'),
                //          string(credentialsId: 'aws-secret-access-key', variable: 'AWS-SECRET-ACCESS-KEY')]) {
                /* groovylint-disable-next-line LineLength */
                withCredentials([usernamePassword(credentialsId: 'AWS-ACCESS-KEY', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                        aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                        aws configure set default.region us-east-1
                    '''

                    echo 'Build Succeed!!'
                    /* groovylint-disable-next-line LineLength */
                    // sh 'aws elasticbeanstalk create-application-version --application-name multi-container-app --version-label multi-docker-project --source-bundle S3Bucket=my_bucket_name,S3Key=multi-docker-project.zip'
                    /* groovylint-disable-next-line LineLength */
                // sh 'aws elasticbeanstalk update-environment --environment-name MultiContainerApp-env --version-label multi-docker-project'
                }
            }
        }
    }
}
// JEKINS SCHEDULE SAMPLES
// # Every fifteen minutes (perhaps at :07, :22, :37, :52):
// H/15 * * * *
// # Every ten minutes in the first half of every hour (three times, perhaps at :04, :14, :24):
// H(0-29)/10 * * * *
// # Once every two hours at 45 minutes past the hour starting at 9:45 AM and finishing at 3:45 PM every weekday:
// 45 9-16/2 * * 1-5
// # Once in every two hour slot between 8 AM and 4 PM every weekday (perhaps at 9:38 AM, 11:38 AM, 1:38 PM, 3:38 PM):
// H H(8-15)/2 * * 1-5
// # Once a day on the 1st and 15th of every month except December:
// H H 1,15 1-11 *
