pipeline {
  agent any
  tools { 
      maven 'Alexa-maven' 
  }
  stages {
    stage('Initialize') {
      steps {
        git url:'https://github.com/timmychien/alexa-cicd'
        echo 'Starting the pipeline'
        sh 'mvn clean'
      }
    }
    stage('Build') {
      steps {
        sh 'mvn -Dmaven.test.failure.ignore=true install'
      }
    }
    stage('Test') {
      steps {
        sh 'mvn -Dtest=AlexaControllerTest test'
      }
    }
    stage('Deploy') {
      steps {
        archiveArtifacts 'target/*.war'
        sh 'az login --service-principal -u <client_ID> -p <client_SECRET> -t <tenant_ID>'
        sh 'az storage blob upload --account-name "alexalab" --account-key "<storage_account_key>" --container-name lab-container --file "/var/lib/jenkins/workspace/alexa-cicd_master/target/alexa-cicd-0.0.1-SNAPSHOT.war" --name "alexa-cicd-0.01-SNATSHOT.war" --overwrite=True'
        sh 'az webapp create --resource-group "lab-rg" --plan "lab-service-plan" --name "alexa-cicd-test" --runtime "TOMCAT:10.0-java11"'
      }
    }
  }
}