pipeline {
    agent {
        label "dev"
    }; 
    
    stages {
        stage("Code Clone") {
            steps {
                git url: "https://github.com/lit-with-git/two-tier-flask-app.git", branch: "master"
            }
        }
        stage("Trivy File System Scan") {
            steps {
                sh "trivy fs . -o scanresults.json"
            }
        }
        
        stage("Build") {
            steps {
                sh "docker build -t two-tier-flask-app ."
            }
        }
        stage("Test") {
            steps {
                echo "Developer / Tester tests likh ke dega..."
            }
        }
        stage("Push to Docker Hub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubCreds", 
                    passwordVariable: "dockerHubPass", 
                    usernameVariable: "dockerHubUser"
                )]) {
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag two-tier-flask-app ${env.dockerHubUser}/two-tier-flask-app"
                    sh "docker push ${env.dockerHubUser}/two-tier-flask-app:latest"
                }
            }
        }
        stage("Deploy") {
            steps {
                sh "docker compose up -d --build flask-app"
            }
        }
    }

    post {
        always{
        success {
            script {
                emailext attachmentsPattern: 'scanresults.json', from: 'singhvaibhav032@gmail.com', 
                to: 'singhvaibhav032@gmail.com', 
                body: 'Build Success', 
                subject: 'Build Success'
            }
        }
        failure {
            script {
                emailext attachmentsPattern: 'scanresults.json', from: 'singhvaibhav032@gmail.com', 
                to: 'singhvaibhav032@gmail.com', 
                body: 'Build Failed', 
                subject: 'Build Failed'
            }
        }
          
            sh "find /home/ubuntu/jenkins/workspace -mindepth 1 -type d -exec rm -r {} +"
        }
    }
}
