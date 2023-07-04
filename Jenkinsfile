import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=d8243891-51bd-418b-a3e4-a2ec74baca95',
        'AZURE_TENANT_ID=1fb45707-df96-4026-9976-47d8a53b877d']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'NetworkWatcherRG'
      def webAppName = 'shop1'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'nithin9698', passwordVariable: 'bCy8Q~icb1wqEOpJAkn~tzfh~Nl1jf25hrRNCbCF', usernameVariable: '116bd714-07d5-4c91-8fd0-d7bcfbbf28c2')]) {
       sh '''
          az login --service-principal -u 116bd714-07d5-4c91-8fd0-d7bcfbbf28c2 -p bCy8Q~icb1wqEOpJAkn~tzfh~Nl1jf25hrRNCbCF -t 1fb45707-df96-4026-9976-47d8a53b877d
          az account set -s d8243891-51bd-418b-a3e4-a2ec74baca95
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
