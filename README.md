# jenkins_pipeline
pipeline{
    agent any 
    
    tools {
        maven 'Maven_3.9'
    }
    stages{
        stage('SCM checkout'){
            steps{
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Anandhi0108/java-maven-app.git']])  
            }
        }
        stage('Maven Build'){
            steps{
                            sh 'mvn clean install'
            }
        }
        stage('Sonar scan'){
            steps{
                withSonarQubeEnv("sonar"){
                    sh "${tool("Sonar 4.8.0")}/bin/sonar-scanner \
                    -Dsonar.host.url=http://ec2-13-233-119-226.ap-south-1.compute.amazonaws.com:9000/ \
                    -Dsonar.login=sqp_0737bad1c08bdde76660c1f4080031805a616049 \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=java-maven-app"
                }
            }
        }
        stage("Nexus upload"){
            steps{
                sh 'mvn -s settings.xml clean deploy'
            }
        }
    }
}
