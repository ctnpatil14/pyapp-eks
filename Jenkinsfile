pipeline {
    agent any
    stages {
        stage("Git checkout") {
            steps {
                git url: "https://github.com/ctnpatil14/pyapp-eks.git", branch: "main"
            }
        }
        stage ("Docker build") {
            steps {
                sh "docker build -t chtnpatil14/pyapps:${env.BUILD_NUMBER} ."
            }
        }
        stage ("Push to Docker hub") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'pwd', usernameVariable: 'usr')]) {
                    sh "docker login -u ${usr} -p ${pwd}"
                    sh "docker push chtnpatil14/pyapps:${env.BUILD_NUMBER}"
                }
            }
        }
        stage("Update dyanmic image Values")
        {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                    sh """
        git config user.name "${GIT_USER}"
        git config user.email "${GIT_USER}@users.noreply.github.com"
        git remote set-url origin https://${GIT_USER}:${GIT_TOKEN}@github.com/ctnpatil14/pyapp-eks.git
        # Update image tag using sed
        sed -i 's|image: chtnpatil14/pyapps:.*|image: chtnpatil14/pyapps:${BUILD_ID}|' k8s/pyapp-deployment.yml
        git add k8s/pyapp-deployment.yml
        git commit -m "Update image tag to ${BUILD_ID}"
        # Use token-only remote URL
        git remote set-url origin https://${GIT_TOKEN}@github.com/ctnpatil14/pyapp-eks.git
        git push origin main
        """
                }
            }
        }
    }
}
