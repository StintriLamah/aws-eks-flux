pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="120042965046"
        AWS_DEFAULT_REGION="eu-west-1"
        IMAGE_REPO_NAME="devops-test"
        IMAGE_TAG="test-${BUILD_NUMBER}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    
    }


    stages{
              stage('Clone repository') {
      

        checkout scm
    }
            

            stage('Docker build and login') {
            steps {
                script {
                    try {
                        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',credentialsId: 'devops-test',accessKeyVariable: 'AWS_ACCESS_KEY_ID',secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']])
                            {

                                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                                sh "docker build -t ${IMAGE_REPO_NAME}:${IMAGE_TAG} ./react-app"
                            }

                    }
                    catch(Exception e) {
                            echo "Test execution failed with error: ${e}"
                            currentBuild.result = 'FAILURE'
                    }
                }


        }


            }


    // Uploading Docker images into AWS ECR
    
    stage('Pushing to ECR') {
     steps{
         script {


            sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:${IMAGE_TAG}"
            sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"


         }
        }
    }
    
    }

}
