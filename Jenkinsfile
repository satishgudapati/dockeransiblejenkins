pipeline{
    agent any

    environment {
      DOCKER_TAG = getVersion()
    }
    
    stages{
        stage('SCM'){
            steps{
                git credentialsId: 'github', 
                    url: 'https://github.com/satishgudapati/dockeransiblejenkins.git'
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t optimisticsatish/javamavendocker:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Push to Registry'){
            
            steps{
                withCredentials([string(credentialsId: 'docker', variable: 'passwd')]) {
                    sh "docker login -u satish200691 -p ${passwd}"
                }
                sh "docker push optimisticsatish/javamavendocker:${DOCKER_TAG} "
            }
        }
        stage('Deploying to server'){
            steps{
               ansiblePlaybook credentialsId: 'DeploymentServer', disableHostKeyChecking: true, extras: '-e DOCKER_TAG=${DOCKER_TAG}', installation: 'ansible', inventory: 'inventory', playbook: 'ansibleplaybook'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
