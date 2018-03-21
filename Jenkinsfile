pipeline {
  agent {
    node {
      label 'localhost_vagrant'
    }

  }
  stages {
    stage('Get Oracle Docker Sources') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: 'origin/master']], doGenerateSubmoduleConfigurations: false, extensions: [[$class: 'CloneOption', depth: 0, noTags: true, reference: '', shallow: false, timeout: 30]], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'e7677693-d5e6-463f-b6d4-597e0fb91c3f', url: 'https://github.com/oracle/docker-images.git']]])
      }
    }
    stage('Build Oracle Docker Images') {
      steps {
        parallel(
          "Build Database EE 12.2.0.1": {
            dir(path: 'OracleDatabase/SingleInstance/dockerfiles') {
              sh 'if [ ! -f 12.2.0.1/linuxx64_12201_database.zip ]; then cp /software/Oracle/Database/linuxx64_12201_database.zip 12.2.0.1/linuxx64_12201_database.zip; fi'
              sh 'sudo ./buildDockerImage.sh -v 12.2.0.1 -e -i'
              sh 'docker tag oracle/database:12.2.0.1-ee localhost:5000/oracle/database:12.2.0.1-ee'
              sh 'docker push localhost:5000/oracle/database:12.2.0.1-ee'
            }
          }
        )
      }
    }
    stage('Cleanup') {
      steps {
        parallel(
          "Cleanup Database EE 12.2.0.1": {
            sh 'docker rmi --force localhost:5000/oracle/database:12.2.0.1-ee'
            sh 'docker rmi --force oracle/database:12.2.0.1-ee'
          }
        )
      }
    }

  }
}
