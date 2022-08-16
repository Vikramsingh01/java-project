pipeline {
    agent none
    
    environment {
    MAJOR_VERSION = 1
  }
    
  stages {
        stage('Say Hello') {
            agent {
            label 'SlaveL1'
            }

      steps {
        echo 'Awesome Student!'
      }
    }
      
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
       }}
  }

   stage('deploy') {
       agent {
    label 'apache'
    }
      steps {
          sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
          sh "cp dist/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      } }
    stage("Running on master built in node") {
      agent {
        label 'apache'
      }
      steps {
        sh "wget http://34.224.223.135:8081/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.MAJOR_VERSION}.${env.BUILD_NUMBER}.jar"
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
        echo "Stashing Any Local Changes"
        sh 'git stash'
        echo "Checking Out Development Branch"
        sh 'git pull origin development'
        sh 'git checkout development'
        echo 'Checking Out Master Branch'
        sh 'git pull origin'
        sh 'git checkout master'
        echo 'Merging Development into Master Branch'
        sh 'git merge development'
        echo 'Pushing to Origin Master'
        sh 'git push origin master'
      }
       } }
 }

