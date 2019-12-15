node('master'){
  echo "GitHub BranhName ${env.BRANCH_NAME}"
  echo "Jenkins Job Number ${env.BUILD_NUMBER}"
  echo "Jenkins Node Name ${env.NODE_NAME}"
  
  echo "Jenkins Home ${env.JENKINS_HOME}"
  echo "Jenkins URL ${env.JENKINS_URL}"
  echo "JOB Name ${env.JOB_NAME}"
  
  properties([
    buildDiscarder(logRotator(numToKeepStr: '3')),
    pipelineTriggers([
    pollSCM('* * * * *')
    ])
  ])
def mavenHome = tool name:"maven-3.6.3"
stage('code checkout ')
{
git credentialsId: '04d0013d-e0cb-4e6e-858f-a818bd2f4918', url: 'https://github.com/hema-creator/maven-web-application.git'
}
stage('build')
{
sh "${mavenHome}/bin/mvn clean package"
}
stage('sonarqube report')
{
sh "${mavenHome}/bin/mvn sonar:sonar"
}
stage('upload artifactory into nexus')
{
sh "${mavenHome}/bin/mvn deploy"
}
stage('deploy app into tomcat')
{
    sshagent(['ebe565f6-04de-498b-9064-617f9b20e8bc']){
    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@35.154.119.223:/opt/apache-tomcat-9.0.29/webapps/maven-web-application.war"
 }
}
stage('emailnotification')
{
    emailext body: '''pipeline build over,
regards,
hema.r''', subject: 'build is over', to: 'hemaravi96@gmail.com'
}
}
