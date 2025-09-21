pipeline {
  agent {
    label 'docker'
  }
  parameters {
    booleanParam(name: 'BUILD_DOCKER', defaultValue: true, description: 'Build docker image')
    choice(name: 'ENVIRONMENT', choices: ['dev', 'demo', 'prod'])
    string(name: 'TAG', defaultValue: 'latest')
    gitParameter(name: 'BRANCH', branchFilter: 'origin/(.*)',  defaultValue: 'main', selectedValue: 'DEFAULT')
  }

  environment {
    PRJ_DIR = "/var/www/diary"
    PRJ_NAME = "diary"
    REPO = "anestesia01/blog-go"
    TOKEN = credentials('docker_token')
    GIT_URL = "https://github.com/AnastasiyaGapochkina01/dos-29-cicd_04.git"
  }

  stages {
    stage('Checkout repo'){
      steps {
        git branch: "${params.BRANCH}", url: "${env.GIT_URL}"
      }
    }

    stage('Build docker image'){
      steps {
        script {
          sh """
            docker build -t "${env.REPO}:${env.PRJ_NAME}-${BUILD_ID}" .
          """
        }
      }
    }

    stage('Push image'){
      steps {
        script {
          sh """
            docker login -u anestesia01 -p "${env.TOKEN}"
            docker push "${env.REPO}:${env.PRJ_NAME}-${BUILD_ID}"
          """
        }
      }
    }

    stage('Deploy'){
      steps {
        script {
          sh """
            docker pull "${env.REPO}:${env.PRJ_NAME}-${BUILD_ID}"
            docker run -d -it --name "${env.PRJ_NAME}-backend-1" -p 3000:3000  "${env.REPO}:${env.PRJ_NAME}-${BUILD_ID}"
          """
        }
      }
    }

    stage('Check'){
      steps {
        script {
          sh """
            curl -s -o /dev/null -w "%{http_code}" 127.0.0.1:3000
          """
        }
      }
    }
  }  
}
