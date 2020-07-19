/* This is reusable jenkinsfile for maven project.

Contributors:
Version:1.0.0

This snippet will:

	-Run the source code using maven,
	-Run the code quality using sonar,
	-Run unit test if any,
	-Push the generated artifacts in to Nexus repository

*/


pipeline {

 agent any

	/*
	Properties([

	[$class : 'BuildDiscarderProperty', strategy : [ $class : 'LogRotator', daysToKeepStr: '1', numToKeepStr: '5']],
	pipelineTriggers([githubpush()]), */



	parameters{

	choice(choices: 'Snapshot\nRelease', description: 'Do you need snapshot or release?', name: 'Requested_Action' )
	}


				stages{

				stage('Checkout SCM'){
					steps{
					checkout scm

					}
				}
				stage ('Build and Unit Test'){
					steps{
					withMaven(mavenSettingsConfig: 'Maven-settings-pb1'){

					sh 'mvn clean install -Dmaven.test.skip=true'

					}


					}
					}



				stage ('Sonar Code quality scan'){
					steps{
					withSonarQubeEnv(installationName: 'prod_sonarqube'){
					withMaven(mavenSettingsConfig: 'Maven-settings-pb1'){


					sh 'mvn -B sonar:sonar '
					//sh 'mvn -B sonar:sonar -Dsonar.branch.name=$BRANCH_NAME'

					}
					}

					}
					}

				stage ('Upload application artifacts to Nexus'){
					steps {
					withMaven(mavenSettingsConfig: 'Maven-settings-pb1'){
					withCredentials([usernamePassword(credentialsId: 'NEXUS_CREDENTIALS', passwordVariable: 'NEXUS_PASSWORD', usernameVariable: 'NEXUS_USERNAME')]){
					withCredentials([usernamePassword(credentialsId: 'GIT_CRED', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]){
					  script {

					 if (params.Requested_Action=='Release'){
                    //-Dresume=false -DuseReleaseProfile=false
					sh """
					 git config user.email 'pankajbhalerao2021@gmail.com'
					 git checkout $BRANCH_NAME
					 mvn release:clean release:prepare release:perform -Dmaven.test.skip=true -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true

                        """
					 }

					 else{

					sh 'mvn deploy -Dmaven.test.skip=true -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true'

					}
					 }


					}
					}
					}
					}
					}







	}
	}
