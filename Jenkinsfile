pipeline {
  agent any
  environment {
    AWS_REGION = "us-east-1"
    ENVIRONMENT = "${params.ENVIRONMENT}"   
    GIT_URL = "${params.GIT_URL}"
    FUNCTION = "${params.FUNCTION}"
    LOWERCASE_FUNCTION = "${params.FUNCTION}".toLowerCase()
    STACK_NAME = "${LOWERCASE_FUNCTION}" + "-" + "stack"      
  }
  stages {
    stage('Install sam-cli') {
      steps {
        //sh 'python3 -m venv venv'
        sh 'python3 -m venv venv && venv/bin/pip3 install aws-sam-cli'
        stash includes: '**/venv/**/*', name: 'venv'
      }
    }
    stage('Initiate'){
	    steps{
			echo "do_checkout() function started for Environment:- ${ENVIRONMENT}"
	        do_checkout()        
	    }      
    }
  }
}


def do_checkout(){
    if(ENVIRONMENT=="DEV"){
	    stage('DEV Git Checkout') {
	      git branch: '$ENVIRONMENT', credentialsId: 'd6aa4510-b9b1-44d1-b0ff-e9d3de1d5428', url: GIT_URL
	    }
    }else if (ENVIRONMENT=="PROD"){
        stage('PROD Git Checkout') {
	      git branch: '$ENVIRONMENT', credentialsId: 'd6aa4510-b9b1-44d1-b0ff-e9d3de1d5428', url: GIT_URL
	    }
    }
}
