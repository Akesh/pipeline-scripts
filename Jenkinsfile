pipeline {
  agent any
  environment {
    AWS_REGION = "us-east-1"
    ENVIRONMENT = "${params.ENVIRONMENT}"
    GIT_URL = "${params.GIT_URL}"
    FUNCTION = "${ENVIRONMENT}"+ "-" +"${params.FUNCTION}"
    LOWERCASE_FUNCTION = "${params.FUNCTION}".toLowerCase()
    LOWERCASE_ENVIRONMENT = "${params.ENVIRONMENT}".toLowerCase()
    STACK_NAME = "${LOWERCASE_ENVIRONMENT}"+ "-" +"${LOWERCASE_FUNCTION}" + "-" + "stack"
  }
  stages {
    stage('Install sam-cli') {
      steps {
        sh 'python3 -m venv venv && venv/bin/pip3 install aws-sam-cli'
        stash includes: '**/venv/**/*', name: 'venv'
      }
    }
    stage('Initiate') {
      steps {
        echo "do_checkout() function started for Environment:- ${ENVIRONMENT}"
        do_checkout()
      }
    }
    stage('Build') {
      steps {
        unstash 'venv'
        //Read AWS SSM parameter store parameters 
        withAWSParameterStore(credentialsId: 'BlazePulsePipelineCredentials', naming: 'relative', path: "/${ENVIRONMENT}", recursive: true, regionName: "${AWS_REGION}") {
          sh 'venv/bin/sam build'
          stash includes: '**/.aws-sam/**/*', name: 'aws-sam'
        }
      }
    }
    stage('Package') {
      steps {
        unstash 'venv'
        //Read AWS SSM parameter store parameters 
        withAWSParameterStore(credentialsId: 'BlazePulsePipelineCredentials', naming: 'relative', path: "/${ENVIRONMENT}", recursive: true, regionName: "${AWS_REGION}") {
          dir("${env.WORKSPACE}/${FUNCTION}") {
            sh "zip -qr ${BUILD_ID}.zip *"
            sh "ls *.zip"
          }
          sh 'aws cloudformation package --template template.yaml --s3-bucket ${BUCKET_ARTIFACTORY} --s3-prefix ${ENVIRONMENT}/${FUNCTION}/Artifacts  --force-upload --output-template-file packaged-template.json'
        }
      }
    }
    stage('Deploy') {
      steps {
        //Read AWS SSM parameter store parameters 
        withAWSParameterStore(credentialsId: 'BlazePulsePipelineCredentials', naming: 'relative', path: "/${ENVIRONMENT}", recursive: true, regionName: "${AWS_REGION}") {
          echo "BUCKET_ARTIFACTORY- ${BUCKET_ARTIFACTORY}"
          unstash 'venv'
          unstash 'aws-sam'
          sh 'venv/bin/sam deploy -t packaged-template.json --stack-name ${STACK_NAME} --parameter-overrides ParameterKey=FunctionName,ParameterValue=${FUNCTION} ParameterKey=LambdaAlias,ParameterValue=${ENVIRONMENT} ParameterKey=AutoPublishCodeSha,ParameterValue=${BUILD_ID} --s3-bucket ${BUCKET_ARTIFACTORY} --s3-prefix ${ENVIRONMENT}/${FUNCTION}/Templates --capabilities CAPABILITY_IAM --region ${AWS_REGION}'
          //executePipeline();
        }
      }
    }
  }
}

//Checkout relevant branch based on environment selection
def do_checkout() {
  if (ENVIRONMENT == "DEV") {
    stage('DEV Git Checkout') {
      git branch: '$ENVIRONMENT', credentialsId: 'd6aa4510-b9b1-44d1-b0ff-e9d3de1d5428', url: GIT_URL
    }
  } else if (ENVIRONMENT == "PROD") {
    stage('PROD Git Checkout') {
      git branch: '$ENVIRONMENT', credentialsId: 'd6aa4510-b9b1-44d1-b0ff-e9d3de1d5428', url: GIT_URL
    }
  }
}