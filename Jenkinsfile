pipeline {
    agent any
    environment {
        AWS_ACCOUNT_ID="120042965046"
        AWS_DEFAULT_REGION="eu-west-1"
        IMAGE_REPO_NAME="devops-test"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
    }


    stages{

            stage('Logging into AWS ECR') {
            steps {
                script {
                    try {
                        withCredentials([[$class: 'AmazonWebServicesCredentialsBinding',credentialsId: 'devops-test',accessKeyVariable: 'AWS_ACCESS_KEY_ID',secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']])
                            {

                                sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"
                            }

                    }
                    catch(Exception e) {
                            echo "Test execution failed with error: ${e}"
                            currentBuild.result = 'FAILURE'
                    }
                }
            }
        }




        stage('Clean Workspace') {
            steps {
                script {
                    // Cleanup before starting the stage
                    cleanWs()
                }
            }
        }

        // stage('Clone Repository') {
        //     steps {
        //         script {
        //             final scmVars = checkout(scm)
        //             env.BRANCH_NAME = scmVars.GIT_BRANCH
        //             env.SHORT_COMMIT = "${scmVars.GIT_COMMIT[0..7]}"
        //             env.GIT_REPO_NAME = scmVars.GIT_URL.replaceFirst(/^.*\/([^\/]+?).git$/, '$1')
        //         }
        //     }
        // }


    // stage('SonarQube Analysis') {
    //     steps {
    //         script {
    //             withSonarQubeEnv('SonarQube') {
    //                 withMaven(maven: 'M3') {
    //                     withEnv(["JAVA_HOME=/usr/java/jdk-17.0.5"])  {
    //                         sh "echo $JAVA_HOME"
    //                         sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:3.10.0.2594:sonar"
    //                     }
    //                 }
    //             }
    //         }
    //     }
    // }

    // stage('Veracode SCA scan') {
    //     steps {
    //         script{
    //             withEnv([ 'scan_collectors=maven', 'compile_first=false', 'install_first=false']){
    //                 withMaven(maven: 'M3') {
    //                     withCredentials([string(credentialsId: 'SRCCLR_API_TOKEN', variable: 'SRCCLR_API_TOKEN')]) {
    //                         sh 'curl -sSL https://download.sourceclear.com/ci.sh | sh'
    //                 }
    //             }
    //         }
    //     }
    // }
    // }

    // stage('Maven Clean and Package') {
    //     steps {
    //         script {
    //             sh "./mvnw clean"
    //             sh "./mvnw package"
    //         }
    //     }
    // }


    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
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
