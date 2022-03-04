node() {
    def repoURL = 'https://github.com/Assilhizaoui/ProjectChannel.git'
   
	stage('Checkout Self') {
        git branch: 'master', credentialsId: '', url: repoURL
    }
    stage('Cucumber Tests') {
        withMaven(maven: 'maven3.8.4') {
		    bat	'mvn clean test'
        }
    }
  stage('Expose report') {
        archive "**/cucumber.json"
        cucumber '**/cucumber.json'
    }
	stage('Import results to Xray') {

		def description = "[BUILD_URL|${env.BUILD_URL}]"
		def labels = '["TNR","automated_regression","end2end"]'
		def environment = "DEV"
		def testExecutionFieldId = 10005
		def testEnvironmentFieldName = "customfield_10136"
		def projectKey = "CHA"
		def xrayConnectorId = '30b960a7-8d25-4744-b9d2-ac3e7bb531b4'
		def info = '''{
				"fields": {
					"project": {
					"key": "''' + projectKey + '''"
				},
				"labels":''' + labels + ''',
				"description":"''' + description + '''",
				"summary": "Automated Regression Execution @ ''' + env.Build_Timestamp + ' ' + environment + ''' " ,
				"issuetype": {
				"id": "''' + testExecutionFieldId + '''"
				},
				"''' + testEnvironmentFieldName + '''" : [
				"''' + environment + '''"
				]
				}
				}'''

			echo info

			step([$class: 'XrayImportBuilder', endpointName: '/cucumber/multipart', importFilePath: 'json/cucumber.json', importInfo: info, inputInfoSwitcher: 'fileContent', serverInstance: xrayConnectorId])
		}
	stage('Send Email') {
        emailext attachmentsPattern: 'target\cucumber-reports\Chenel.html',
                subject: "Channel Site Test Automation - ${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful",
                to: "assil.jenkins@gmail.com"
    }
	
}
