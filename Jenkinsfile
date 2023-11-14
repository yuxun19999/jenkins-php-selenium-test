// pipeline {
// 	agent none
// 	stages {
// 		stage('Integration UI Test') {
// 			parallel {
// 				stage('Deploy') {
// 					agent any
// 					steps {
// 						sh './jenkins/scripts/deploy.sh'
// 						input message: 'Finished using the web site? (Click "Proceed" to continue)'
// 						sh './jenkins/scripts/kill.sh'
// 					}
// 				}
// 				stage('Headless Browser Test') {
// 					agent {
// 						docker {
// 							image 'maven:3-alpine' 
// 							args '-v /root/.m2:/root/.m2' 
// 						}
// 					}
// 					steps {
// 						// sh 'mvn -B -DskipTests clean package'
// 						sh 'mvn test'
// 					}
// 					post {
// 						always {
// 							junit 'target/surefire-reports/*.xml'
// 						}
// 					}
// 				}
// 			}
// 		}
// 	}
// }

pipeline { 
	agent any
	stages {
		stage ('Checkout') {
			steps {
				git branch:'master', url: 'https://github.com/ScaleSec/vulnado.git'
				} 
			}
		stage ('Build') { steps {
			sh '/var/jenkins_home/apache-maven-3.6.3/bin/mvn --batch-mode -V -U -e clean verify -Dsurefire.useFile=false -Dmaven.test.failure.ignore'
			} 
		}
		stage ('Analysis') { steps {
			sh '/var/jenkins_home/apache-maven-3.6.3/bin/mvn --batch-mode -V -U -e checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs'
			} 
		}
	} 
	post {
		always {
			junit testResults: '**/target/surefire-reports/TEST-*.xml'
			recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()] recordIssues enabledForFailure: true, tool: checkStyle()
			recordIssues enabledForFailure: true, tool: spotBugs(pattern:'**/target/findbugsXml.xml')
			recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml') recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
		} 
	}
}