  pipline {
    agent none

    environment {
      MAJOR_VERSION = 1
    }

    stages {
      stage('Unit Tests') {
        agent {
          label 'master'
        }
        steps {
          sh 'ant -f test.xml -v'
          junit 'reports/result.xml'
        }
      }
      stage('build') {
        agent {
          label 'master'
        }
        steps {
          sh 'ant -f build.xml -v'
        }
        post {
          success {
            archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
          }
        }
      }
      stage('deploy') {
        agent {
          label 'master'
        }
        steps {
          sh "if ![ -d '/var/www/html/rectangles/all/${env.BRANCH_NAME}' ]; then mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}; fi"
          sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
        }
      }
      stage("Running on Slave") {
        agent {
          label 'Slave'
        }
        steps {
          sh "wget http://172.16.88.143/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
          sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
        }
      }
      stage('Promote to Green') {
        agent {
          label 'master'
        }
        when {
          branch 'tags/mytag'
        }
        steps {
          sh "cp /var/ww/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        }
      }
      stage('Promote Development Branch to Master') {
        agent {
          label 'master'
        }
        when {
          branch 'multi'
        }
        steps {
          echo "Stashing Any Local Changes"
          sh 'git stash'
          echo "Checking Out Development Branch"
          sh 'git checkout development'
          echo 'Checking Out Master Branch'
          sh 'git pull origin'
          sh 'git checkout master'
          echo 'Merging Development into Master Branch'
          sh 'git merge development'
          echo 'Pushing to Origin Master'
          sh 'git push origin master'
          echo 'Tagging the Release'
          sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
          sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        }
      }
  }
}
