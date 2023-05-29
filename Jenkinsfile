def projectName = 'nick-todd-springapp-os4'
def version = "0.0.${currentBuild.number}"
def dockerImageTag = "${projectName}:${version}"

pipeline {
  agent any

  stages {
    // Need to login to a Docker registy

     stage('Build docker image') {
          // this stage also builds and tests the Java project using Maven
          steps {
            sh "docker build -t ${dockerImageTag} ."
            // Needs to be pushed to a registry
          }
      }
    stage('Deploy Container To Openshift') {
      environment {
           OPENSHIFT_CREDS = credentials('openshiftCreds')
           //MYSQL_CREDS = credentials('MySQLCreds')
          }
      steps {
        sh "oc login ${OPENSHIFT_SERVER} -u ${OPENSHIFT_CREDS_USR} -p ${OPENSHIFT_CREDS_PSW} --insecure-skip-tls-verify"
        sh "oc project ${projectName} || oc new-project ${projectName}"
        sh "oc delete all --selector app=${projectName} || echo 'Unable to delete all previous openshift resources'"
        // Needs the image to be in the registry
        sh "oc new-app ${dockerImageTag} -l version=${version}"
        sh "oc expose service/${projectName}"
      }
    }
  }
}
