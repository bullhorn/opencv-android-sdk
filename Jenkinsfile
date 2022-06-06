library "EtsJenkinsLibrary@0.5.0"
pipeline {
  agent {
    docker {
      image 'bullhorn-hub.artifactory.bullhorn.com/ets-build-agents:jdk-8-debian'
      args '-v /var/run/docker.sock:/var/run/docker.sock -u root'
      label "bosjenkins-docker-agent"
      reuseNode true
    }
  }

  environment {
    DISPLAY_NAME = "opencv-android-sdk"
    JENKINS_BUILD = "Jenkins Build <${env.BUILD_URL}/|#${env.BUILD_NUMBER}>"
    GITHUB_REPO_NAME = "opencv-android-sdk"
    GITHUB_GROUP = "bullhron"
    GITHUB_URL = "<https://github.com/${env.GITHUB_GROUP}/${env.GITHUB_REPO_NAME}/commits/${env.BRANCH_NAME}|${env.BRANCH_NAME}>"
    SLACK_CHANNEL = "ets-ci-cd"
    SLACK_TOKEN = "EnidWtGz19YW55Qx0wdh4ef5"
    SLACK_ICON = "crescent_moon"
    BUILD_ERROR_MESSAGE = "[${env.DISPLAY_NAME}] *FAILED* to build ${env.JENKINS_BUILD} for ${env.GITHUB_URL}."
    BUILD_SUCCESS_MESSAGE = "[${env.DISPLAY_NAME}] *SUCCESSFULLY* built ${env.JENKINS_BUILD} for ${env.GITHUB_URL}."
    PUBLISH_ERROR_MESSAGE = "[${env.DISPLAY_NAME}] *FAILED* to publish${env.JENKINS_BUILD} for ${env.GITHUB_URL}."
    PUBLISH_SUCCESS_MESSAGE = "[${env.DISPLAY_NAME}] *SUCCESSFULLY* published ${env.JENKINS_BUILD} for ${env.GITHUB_URL}."
  }

  stages {
    stage('Install Android SDK') {
      steps {
        sh "apt install android-sdk android-sdk-platform-30"
        sh "export ANDROID_HOME=/usr/lib/android-sdk"
      }
    }
    stage('Build') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'c376347e-4245-49fc-be2c-b4aa0ddce81f', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
          echo "---------------Building Project---------------"
          sh "gradle clean install -PartifactoryUsername=$USERNAME -PartifactoryPassword=$PASSWORD"
          sh "gradle build -PartifactoryUsername=$USERNAME -PartifactoryPassword=$PASSWORD" 
        }
      }
    }
    stage('Publish') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'c376347e-4245-49fc-be2c-b4aa0ddce81f', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
          echo "---------------Publishing to Artifactory---------------"
          sh 'gradle artifactoryPublish -PartifactoryUsername=$USERNAME -PartifactoryPassword=$PASSWORD'
        }
      }
    }
  }
}

