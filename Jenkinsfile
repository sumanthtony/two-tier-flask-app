pipeline{
    agent {label "dev"}
    
    stages{
        stage("Code Checkout"){
            steps{
                git url: "https://github.com/sumanthtony/two-tier-flask-app.git", branch: "master"
            }
        }
        stage("Trivy File_System scan"){
            steps{
                sh "trivy fs . -o results.json"
            }
        }
        stage("Docker build"){
            steps{
                sh "docker build -f Dockerfile-multistage -t flask-app ."
            }
        }
        stage("Docker login and Push to DockerHub"){
            steps{
                withCredentials([usernamePassword(credentialsId: "docker-creds",
                usernameVariable: "dockerHubUser",
                passwordVariable: "dockerHubPass"
                )]){
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker tag flask-app ${env.dockerHubUser}/two-tier-flask-app:latest"
                    sh "docker push ${env.dockerHubUser}/two-tier-flask-app:latest"
                }
            }
        }
        stage("Deploy the App"){
            steps{
                sh "docker compose up -d --build flask-app"
            }
        }
    }
}
