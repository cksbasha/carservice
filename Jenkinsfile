pipeline {

  environment {
    PROJECT = "indigo-history-337312"
    APP_NAME = "cartservice"
    FE_SVC_NAME = "${APP_NAME}-cart"
    CLUSTER = "way2die"
    CLUSTER_ZONE = "us-east4-b"
    IMAGE_TAG = "gcr.io/${PROJECT}/${APP_NAME}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"
    JENKINS_CRED = "${PROJECT}"
  }

  agent {
    kubernetes {
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
  component: ci
spec:
  # Use service account that can deploy to all namespaces
  # serviceAccountName: cd-jenkins
  containers:
  - name: golang-bld
    image: golang:1.10
    command:
    - cat
    tty: true
  - name: gcloud
    image: gcr.io/cloud-builders/gcloud
    command:
    - cat
    tty: true
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
"""
}
  }
  stages {
    stage('codebuild') {
      steps {
        container('golang') {
          sh """
            ln -s `pwd`
            go codebuild
          """
        }
      }
    }
    stage('Build and push image with Container Builder') {
      steps {
        container('gcloud') {
          sh "PYTHONUNBUFFERED=1 gcloud builds submit -t ${IMAGE_TAG} ."
        }
      }
    }    
    stage('Deploy Dev') {
      // Developer Branches
      when {
        not { branch 'main' }
        not { branch 'canary' }
      }
      steps {
        container('kubectl') {
        }
      }
    }
  }
}
