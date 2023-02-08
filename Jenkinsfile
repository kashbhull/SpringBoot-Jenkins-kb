pipeline {
	agent any
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
		stage('War Build'){
			steps{
			sh '''
			mvn clean package
			echo 'Kash wasnt ere'
			'''
			}
		}
		stage('Moving War'){
			steps{
			sh '''
			mkdir -p ./wars
			mv ./target/*.war ./wars/project_war.war
			'''
			}
        }
		stage('Build Docker Image'){
			steps{
			sh '''
			docker build -t eu.gcr.io/lbg-cloud-incubation/springdemokb:latest .
			'''
			}
        }
		stage('Push Docker Image'){
			steps{
			sh '''
			docker push eu.gcr.io/lbg-cloud-incubation/springdemokb:latest
			'''
			}
        }
		stage('Stopping Container'){
			steps{
				script {
					if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.123.249.236 << EOF
						docker rm -f javabuild
						'''
					} else if ("${GIT_BRANCH}" == 'origin/feature') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.132.202.226 << EOF
						docker rm -f javabuild
						'''
					}
				}
			}
		}
		stage('Restart App'){
			steps{
				script {
					if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.123.249.236 << EOF
						docker run -d -p 8080:8080 --name javabuild  eu.gcr.io/lbg-cloud-incubation/springdemokb:latest
						'''
					} else if ("${GIT_BRANCH}" == 'origin/feature') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.132.202.226 << EOF
						docker run -d -p 8080:8080 --name javabuild  eu.gcr.io/lbg-cloud-incubation/springdemokb:latest
						'''
					}
				}
			}
		}

	}
}
