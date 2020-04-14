/**********************************************************************************
 * JenkinsFile for ProgramAB
 *
 * for adjusting build number for specific branch build
 * Jenkins.instance.getItemByFullName("ProgramAB-multibranch/develop").updateNextBuildNumber(185)
 *  
 * CHANGE build.properties TO BUILD AND DEPLOY A NEW BUILD
 *
 ***********************************************************************************/
// [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/MyRobotLab/ProgramAB/']

properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '3')), [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/MyRobotLab/ProgramAB/'], pipelineTriggers([pollSCM('* * * * *')])])

   
// node ('ubuntu') { // use any node
node ('master') { // use any node

   def version = "1.3.${env.BUILD_NUMBER}" 
   def groupId = "program-ab"
   def artifactId = "program-ab-data"
   def serviceName = "ProgramAB"

   // deployment variables
   def path = groupId.replace(".","/") + "/" + artifactId.replace(".","/")
   def repo = "/repo/artifactory/myrobotlab/" + path + "/" 

   stage('clean') { 
      echo 'clean the workspace'
      // deleteDir()
      cleanWs()
   }

   stage('check out') { 
      // checkout scm - apparently below "polls" what is the rate?
      git "https://github.com/MyRobotLab/${serviceName}.git"
   }

   stage('build') { 
      sh 'echo \"' + version + '\" > resource/${serviceName}/version.txt'
   }

   stage('zip') {
   
        sh "zip -r ${artifactId}-${version}.zip resource"
        // archiveArtifacts artifacts: 'test.zip', fingerprint: true    
	}

   /**
    * deployment locally by installing into maven like repo with nginx serving the repo directory
    */
   stage('install') {
      sh "mkdir -p ${repo}${version}"

      sh "cp inmoov2-${version}.zip ${repo}${version}"

      // inmoov2-{version}.pom
      def depFileName = repo + version + "/" + artifactId + "-" + version + ".pom"
      echo "writing pom " + depFileName
      File file = new File(depFileName)
      file.write("<project>")
      file.append("<modelVersion>4.0.0</modelVersion>")
      file.append("<groupId>"+groupId+"</groupId>")
      file.append("<artifactId>"+artifactId+"</artifactId>")
      file.append("<version>"+version+"</version>")
      file.append("<description>Bots main service module for ${serviceName} compatible with Nixie release of myrobotlab</description>")
      file.append("<url>http://myrobotlab.org</url>")
      file.append("</project>")
     }
}
