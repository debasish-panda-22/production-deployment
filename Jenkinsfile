pipeline {
    agent {
        label 'Jenkins-node-01'
    }

    environment {
        APP_NAME = "complete-production-e2e-pipeline"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/debasish-panda-22/production-deployment'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh '''
                cat deployment.yaml
                sed -i 's/${APP_NAME}:.*$/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml
                cat deployment.yaml
                '''
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh '''
                git config --global user.name "debasish-panda-22"
                git config --global user.email "debasishpanda26032001@gmail.com"
                git add deployment.yaml
                git commit -m "Updated Deployment Manifest"
                '''
                
                withCredentials([gitUsernamePassword(credentialsId: 'github', gitToolName: 'Default')]) {
                    sh "git push https://github.com/debasish-panda-22/production-deployment main"
                }
            }
        }
    }
}
