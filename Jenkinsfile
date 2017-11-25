pipeline {
  agent none

  environment {
    MAJOR_VERSION = 1
  }

  stages {
    stage('Unit Tests') {
      steps {
        sh 'ant -f jenkins/exercises/java-project/test.xml -v'
        junit 'jenkins/exercises/java-project/reports/result.xml'
      }
      agent {
        label 'apache'
      }
    }
    stage('build') {
      steps {
        sh 'ant -f jenkins/exercises/java-project/build.xml -v'
      }
      agent {
        label 'apache'
      }
      post {
        always {
          archiveArtifacts artifacts: 'jenkins/exercises/java-project/dist/*.jar', fingerprint: true
        }
      }
    }
    stage('deploy') {
      steps {
        sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "cp jenkins/exercises/java-project/dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
      agent {
        label 'apache'
      }
    }
    stage('Running on CentOS') {
      steps {
        sh "wget http://bdspecht1.mylabserver.com/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
      agent {
        label 'CentOS'
      }
    }
    stage('Running on Debian') {
      steps {
        sh "wget http://bdspecht1.mylabserver.com/rectangles/all/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
      agent {
        docker 'openjdk:8u151-jre'
      }
    }
    stage('Promote to Green') {
      agent {
        label 'apache'
      }
      when {
        branch 'master'
      }
      steps {
        sh "cp /var/www/html/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
      }
    }
    stage('Promote Development Branch to Master') {
      agent {
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps {
        echo "Stashing any local changes"
        sh "git stash"
        echo "Checking out development branch"
        sh "git checkout development"
        echo "Checking out master branch"
        sh "git checkout master"
        echo "Merging development into master branch"
        sh "git merge development"
        echo "Pushing to origin"
        sh "git push origin master"
        echo "Tagging the Release"
        sh "git tag ${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        sh "git push origin ${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
    }
  }
}
