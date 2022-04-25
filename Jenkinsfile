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
  }
  stages {    
    stage('Initiate') {
      steps {
        echo "do_checkout() function started for Environment:- ${ENVIRONMENT}"
        do_checkout()
        script {            
            echo 'Deployment Strategy - 'get_deployment_strategy()
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

//Decide deployment strategy based on the branch selection
def get_deployment_strategy() {
  ALL_AT_ONCE_DEPLOYMENT= "AllAtOnce"
  CANARY_DEPLOYMENT= "Canary10Percent5Minutes"
  if (ENVIRONMENT == "DEV") {
    return ALL_AT_ONCE_DEPLOYMENT
  } else if (ENVIRONMENT == "PROD") {
  	return CANARY_DEPLOYMENT
  }
  return ALL_AT_ONCE_DEPLOYMENT
}