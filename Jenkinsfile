pipeline {
    agent {
        label "jenkins-agent"
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
                git branch: 'main', url: 'https://github.com/shivamrai27/gitops-e2e-production-pipeline.git'
            }
        }

        stage("Update the Deployment Tags") {
            steps {
                sh """
  
                    cat deployment.yaml || echo "deployment.yaml not found!"

                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yaml

                    
                    cat deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config --global user.name "shivamrai27"
                    git config --global user.email "200shivamrai@gmail.com"
                    git add deployment.yaml || echo "Nothing to add"
                    git commit -m "Updated Deployment Manifest" || echo "No changes to commit"
                    
                    git push https://github.com/shivamrai27/gitops-e2e-production-pipeline.git main || echo "Push skipped or failed (no auth)"
                """
            }
        }
    }
}
