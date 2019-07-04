
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
        container(name: 'kaniko', shell: '/busybox/sh') {
          //error 'Fake error to force failure in Build'
          sh 'echo "building artifact"'
          sh """#!/busybox/sh
                /kaniko/executor --context `pwd` --destination eu.gcr.io/emea-sa-demo/petclinic-kaniko:latest --cache=true
          """
          publishEvent simpleEvent('dcanadillas/kaniko-petclinic:latest')
        }
      }
    }
  }
}

