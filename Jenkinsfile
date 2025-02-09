pipeline {
	agent any
	environment {
		appIP="34.123.249.236";
		containerName="java";
		imageName="javakb3";
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
			docker build -t eu.gcr.io/lbg-cloud-incubation/$imageName:latest .
			'''
			}
		}
		stage('Push Images'){
			steps{
			sh '''
			docker push eu.gcr.io/lbg-cloud-incubation/$imageName:latest
			'''
			}
                }

		stage('Stopping Container'){
			steps{
			sh '''ssh -i "~/.ssh/id_rsa" jenkins@$appIP << EOF
			docker rm -f $containerName
			'''
			}
		}
		stage('Restart App'){
			steps{
			sh '''ssh -i "~/.ssh/id_rsa" jenkins@$appIP << EOF
			docker run -d -p 8080:8080 --name $containerName  eu.gcr.io/lbg-cloud-incubation/$imageName
			'''
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
