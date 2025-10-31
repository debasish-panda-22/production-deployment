pipeline {
  agent { label 'Jenkins-node-01' }

  // accept IMAGE_TAG from upstream buildWithParameters
  parameters {
    string(name: 'IMAGE_TAG', defaultValue: '', description: 'Image tag passed from CI job')
  }

  environment {
    APP_NAME = "complete-production-e2e-pipeline"
  }

  stages {
    stage("Cleanup Workspace") {
      steps { cleanWs() }
    }

    stage("Checkout from SCM") {
      steps {
        git branch: 'main', credentialsId: 'github', url: 'https://github.com/debasish-panda-22/production-deployment'
      }
    }

    stage("Update the Deployment Tags") {
      steps {
        // Use triple double-quotes so ${IMAGE_TAG} is expanded by the shell
        sh """
          echo "Before:"
          cat deployment.yaml
          # Replace the tag — double quotes allow shell expansion of ${IMAGE_TAG}
          sed -i "s|${APP_NAME}:.*\$|${APP_NAME}:${IMAGE_TAG}|g" deployment.yaml
          echo "After:"
          cat deployment.yaml
        """
      }
    }

    stage("Push the changed deployment file to Git") {
      steps {
        // set git user/email
        sh '''
          git config --global user.name "debasish-panda-22"
          git config --global user.email "debasishpanda26032001@gmail.com"
        '''

        // Use credentials and only push if there are changes
        withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_PASS')]) {
          sh '''
            if git status --porcelain | grep -q .; then
              echo "Changes detected — committing and pushing"
              git add deployment.yaml
              git commit -m "Updated Deployment Manifest - ${IMAGE_TAG}"
              # push using credential in URL (masked in logs)
              git push https://${GIT_USER}:${GIT_PASS}@github.com/debasish-panda-22/production-deployment main
            else
              echo "No changes to commit — skipping push"
            fi
          '''
        }
      }
    }
  }
}
