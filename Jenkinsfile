pipeline {
	agent any
	environment {
		DEPLOY_CREDS = credentials('deploy-anypoint-app')
		WORKER = "MICRO"
		MULE_KEY = "poc"
	}
	stages {
		stage('Build') {
			steps {
				configFileProvider([configFile(fileId: "MVN_GLBL_SETT_ID", variable: 'MAVEN_SETTINGS_XML')]) {
					bat 'mvn -s "%MAVEN_SETTINGS_XML%" clean -DskipTests package'
				}
			}
		}
		
		stage('Unit Test') {
			steps {
				configFileProvider([configFile(fileId: "MVN_GLBL_SETT_ID", variable: 'MAVEN_SETTINGS_XML')]) {
					bat 'mvn -s "%MAVEN_SETTINGS_XML%" clean test'
				}
			}
		}
		
		stage('Deploy Development') {
			when {
				branch 'develop'  //only run these steps on the develop branch
			}
			environment {
				ENVIRONMENT_CREDS = credentials('anypoint-sandbox-cred')
				ENVIRONMENT = 'Sandbox'
				APP_NAME = 'cicd-poc-uky-api'
			}
			steps {
				configFileProvider([configFile(fileId: "MVN_GLBL_SETT_ID", variable: 'MAVEN_SETTINGS_XML')]) {
					bat 'mvn -s "%MAVEN_SETTINGS_XML%" -DskipTests clean deploy -DmuleDeploy -DgrantType="client_credentials" -DclientId="%DEPLOY_CREDS_USR%" -DclientSecret="%DEPLOY_CREDS_PSW%" -DapplicationName="%APP_NAME%" -Denvironment="%ENVIRONMENT%" -DworkerType="%WORKER%" -Danypoint.platform.client_id="%ENVIRONMENT_CREDS_USR%" -Danypoint.platform.client_secret="%ENVIRONMENT_CREDS_PSW%" -Dmule.key="%MULE_KEY%"'
				}
				echo "Deployed to Development"
			}
    	}
    	stage('Deploy UAT/Staging') {
    		when {
    			branch 'staging'  //only run these steps on the staging branch
    			}
    		environment {
    			ENVIRONMENT_CREDS = credentials('anpoint-uat-credentials')
    			ENVIRONMENT = 'UAT'
    			APP_NAME = 'cicd-poc-uat-api'
    		}
    		steps {
    			echo "Deployed to UAT"
    		}
    	}
    	stage('Publish Artifact') {
    		when {
    			branch 'develop'  //only run these steps on the main branch
    		}
    		steps{
    			configFileProvider([configFile(fileId: "MVN_GLBL_SETT_ID", variable: 'MAVEN_SETTINGS_XML')]) {
    				bat 'mvn -s "%MAVEN_SETTINGS_XML%" -DskipTests clean deploy'
    			}
    		}
    	}
    	stage('Deploy Prod') {
    		when {
    			branch 'main'  //only run these steps on the main branch
    		}
    		environment {
    			ENVIRONMENT_CREDS = credentials('anypoint-sandbox-cred')
    			ENVIRONMENT = 'Production'
    			APP_NAME = 'cicd-poc-api'
    		}
    		steps {
    			echo "Deployed to Prod"
    		}
    	}
    }
}
