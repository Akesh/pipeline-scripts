pipeline {
  agent any
  environment {
    AWS_REGION = "us-east-1"
    ENVIRONMENT = "${params.ENVIRONMENT}"
    GIT_URL = "${params.GIT_URL}"
    FUNCTION = "${params.FUNCTION}"
    LOWERCASE_FUNCTION = "${params.FUNCTION}".toLowerCase()
    LOWERCASE_ENVIRONMENT = "${params.ENVIRONMENT}".toLowerCase()
    STACK_NAME = "${LOWERCASE_ENVIRONMENT}"+ "-" +"${LOWERCASE_FUNCTION}" + "-" + "stack"
    BUCKET_ARTIFACTORY = "blazepulse-artifactory-bucket"
    DEPLOYMENT_STRATEGY= "AllAtOnce"
  }
  stages {    
    stage('Initiate') {
      steps {
        echo "do_checkout() function started for Environment:- ${ENVIRONMENT}"
        do_checkout()
        if (ENVIRONMENT == 'DEV') {
        	DEPLOYMENT_STRATEGY ="AllAtOnce"
        } else {
        	DEPLOYMENT_STRATEGY ="Canary"
        }
        echo "Deployment Strategy - ${DEPLOYMENT_STRATEGY}"
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