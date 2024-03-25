pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="120042965046"
        AWS_DEFAULT_REGION="eu-west-1"
        IMAGE_REPO_NAME="devops-test"
        IMAGE_TAG="tests-${BUILD_NUMBER}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    
    }


    stages{
   stage('Checkout') {
            steps {
                
                // Checkout the code from the repository
                script {
                    try {
                        echo "Attempting to checkout code..."
                        checkout scm
                        echo "Code checkout successful"
                    } catch (Exception e) {
                        echo "Failed to checkout code: ${e.message}"
                        error "Failed to checkout code"
                    }
                }
            }
        }
        //     stage('Clone Repository') {
        //     steps {
        //         script {
        //             git url: 'https://github.com/StintriLamah/aws-eks-flux.git',
        //                 branch: 'main'
        //         }
        //     }
        // }
            

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
    // Building Docker images
    // stage('Building image') {
    //   steps{
    //     script {
    //       sh "docker build --network=host -t ${env.GIT_REPO_NAME} ."
    //     }
    //   }
    // }

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
