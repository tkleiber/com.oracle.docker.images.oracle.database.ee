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
          "Build Database EE 18.3.0": {
            dir(path: 'OracleDatabase/SingleInstance/dockerfiles') {
              sh 'if [ ! -f 18.3.0/LINUX.X64_180000_db_home.zip ]; then cp /software/Oracle/Database/LINUX.X64_180000_db_home.zip 18.3.0/LINUX.X64_180000_db_home.zip; fi'
              sh 'sudo ./buildDockerImage.sh -v 18.3.0 -e -i'
              sh 'docker tag oracle/database:18.3.0-ee localhost:5000/oracle/database:18.3.0-ee'
              sh 'docker push localhost:5000/oracle/database:18.3.0-ee'
            }
          }
        )
      }
    }
    stage('Cleanup') {
      steps {
        parallel(
          "Cleanup Database EE 18.3.0": {
            sh 'docker rmi --force localhost:5000/oracle/database:18.3.0-ee'
            sh 'docker rmi --force oracle/database:18.3.0-ee'
          }
        )
      }
    }

  }
}
