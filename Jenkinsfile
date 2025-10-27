pipeline {
    agent {
        label "jenkins-agent"
    }

    environment {
        APP_NAME = "cid-cd-pipeline-proj"
        GITHUB_TOKEN = credentials('GITHUB_TOKEN')  
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
                    echo "=== BEFORE UPDATE ==="
                    cat deployment.yaml
                    
                    echo ""
                    echo "=== UPDATING IMAGE TAG TO: ${IMAGE_TAG} ==="
                    sed -i 's|image: shivamrai27/${APP_NAME}:.*|image: shivamrai27/${APP_NAME}:${IMAGE_TAG}|g' deployment.yaml
                    
                    echo ""
                    echo "=== AFTER UPDATE ==="
                    cat deployment.yaml
                    
                    echo ""
                    echo "=== CHANGES MADE ==="
                    git diff deployment.yaml
                """
            }
        }

        stage("Push the changed deployment file to Git") {
            steps {
                sh """
                    git config user.name "shivamrai27"
                    git config user.email "200shivamrai@gmail.com"
                    
                    git add deployment.yaml
                    
                    if git diff --staged --quiet; then
                        echo "⚠️ No changes detected"
                        exit 0
                    fi
                    
                    git commit -m "Updated Deployment Manifest to ${IMAGE_TAG}"
                    git push https://shivamrai27:${GITHUB_TOKEN}@github.com/shivamrai27/gitops-e2e-production-pipeline.git main
                    
                    echo "✅ Successfully pushed changes to GitHub"
                """
            }
        }
    }
    
    post {
        success {
            echo "✅ Deployment updated successfully to ${IMAGE_TAG}"
        }
        failure {
            echo "❌ Pipeline failed"
        }
        always {
            cleanWs()
        }
    }
}
