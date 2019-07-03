
pipeline {
  agent {
    kubernetes {
        label 'kaniko-build-pod'
        yamlFile 'podTemplate.yaml'
    }
  }
  stages {
    stage('Maven Install') {
      steps {
        container('maven') {
          sh 'mvn clean install'
        }
      }
    }
    stage('Docker Build and Push') {
      steps {
        container('kaniko'){
          //error 'Fake error to force failure in Build'
          // sh '''#!/busybox/sh
          //   /kaniko/executor --context `pwd` --destination eu.gcr.eu/emea-sa-demo/hello-kaniko:latest
          //   '''
          echo 'Using Kaniko to build and push container'
          //sh 'ls'
        }
      }
    }
  }
}
