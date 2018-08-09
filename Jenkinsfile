pipeline {
  agent any

  environment {
    MAJOR_VERSION = 1
  }

  stages {
    stage('build') {
      steps {
        sh 'javac -d . src/*.java'
        sh 'echo Main-Class: Rectangulator > MANIFEST.MF'
        sh 'jar -cvmf MANIFEST.MF rectangle.jar *.class'
      }
      
      post {
        success {
            archiveArtifacts artifacts: 'rectangle.jar', fingerprint: true
        }
      }

    }
    stage('run') {
      steps {
        sh 'java -jar rectangle.jar 7 9'
      }
    }

    stage('Promote Dev to Master branch') {
        when {
            branch 'dev'
        }

        steps {
            echo "Stashing Local Changes"
            sh 'git stash'
            echo "Checking Out Dev"
            sh 'git checkout dev'
            sh 'git pull origin'
            echo "Checking Out Master"
            sh 'git checkout master'
            echo "Merging Dev into Master"
            sh 'git merge dev'
            echo "Git push to Origin"
            sh 'git push origin master'
    
        }
    }

    stage('Tagging the Release') {
      when {
        branch 'master'
      }

      steps {
        sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
      }
    }

  }
}
