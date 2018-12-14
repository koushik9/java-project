pipeline {
  agent none

  environment {
    MAJOR_VERSION = 1
  }
 stages {
    stage('Unit Tests') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build') {
      agent {
        label 'apache'
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
        label 'apache'
      }
      steps {
        sh "if ![ -d '/opt/software/apache/rectangles/all/${env.BRANCH_NAME}' ]; then mkdir /opt/software/apache/rectangles/all/${env.BRANCH_NAME}; fi"
        sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /opt/software/apache/rectangles/all/${env.BRANCH_NAME}/"
      }
    }
    stage("Running on Linux") {
      agent {
        label 'Linux'
      }
      steps {
  sh"chmod 755 /var/www/html/rectangles/all/master/*"
   sh"chmod 777 /var/www/html/rectangles/all/development/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "http://lokeshkatamaneni2c.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"

 sh "chmod 755 /opt/software/apache/rectangles/all/master/*"
   sh "chmod 755 /opt/software/apache/rectangles/all/development/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
        sh "wget ec2-54-221-13-244.compute-1.amazonaws.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"

        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage("Test on Docker") {
      agent {
        docker 'openjdk:8u121-jre'
      }
      steps {

        sh "wget http://lokeshkatamaneni2c.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
=======
        sh "wget ec2-54-221-13-244.compute-1.amazonaws.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"

        sh "java -jar rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar 3 4"
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
        sh "cp /opt/software/apache/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /opt/software/apache/rectangles/green/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
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
      post {
        success {
          emailext(
            subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Development Promoted to Master",
            body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Development Promoted to Master":</p>
            <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
            to: "kowshikdevops@gmail.com"
          )
        }
      }
    }
  }
  post {
    failure {
      emailext(
        subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed!",
        body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!":</p>
        <p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
        to: "kowshikdevops@gmail.com"
      )
    }
  }
}
