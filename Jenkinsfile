pipeline {
  agent slave
  stages {
    stage('build'){
      steps {
        sh "ant -f build.xml -v"
        echo "BUILD STAGE COMPLETED"
      }
    }
  }
}
