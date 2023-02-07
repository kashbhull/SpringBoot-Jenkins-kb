pipeline {
	agent any
	environment {
		appIP="";
		containerName="java";
		imageName="javakb4";
	}
	stages{
		stage('Test Application'){
			steps{
			sh 'mvn clean test'
			}
		}
		stage('Save Tests'){
			steps{
			sh 'mkdir -p /home/jenkins/Tests/${BUILD_NUMBER}_tests/'
			sh 'mv ./target/surefire-reports/*.txt /home/jenkins/Tests/${BUILD_NUMBER}_tests/'
			}
		}
		stage('Build Application'){
			steps{
			sh 'mvn clean package'
			}
		}
		stage('Docker Build'){
			steps{
			sh '''
			docker build -t ksbhull/$imageName:latest .
			'''
			}
		}
		stage('Push Images'){
			steps{
			sh '''
			docker push ksbhull/$imageName:latest
			'''
			}
                }

		stage('Stopping Container'){
			steps{

				script {
					if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@35.226.220.179 << EOF
						docker rm -f $containerName
						'''
					} else if ("${GIT_BRANCH}" == 'origin/development') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@35.225.183.14 << EOF
						docker rm -f $containerName
						'''
					}
			}
		}
		stage('Restart App'){
			steps{

				script {
					if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@35.226.220.179 << EOF
						docker run -d -p 8080:8080 --name $containerName  ksbhull/$imageName
						'''
					} else if ("${GIT_BRANCH}" == 'origin/development') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@35.225.183.14 << EOF
						docker run -d -p 8080:8080 --name $containerName  ksbhull/$imageName
						'''
					}
			}
		}
		stage('Clean Up'){
			steps{
			sh '''
			docker system prune -f
			'''
			}
		}
	}
}
	}
}
