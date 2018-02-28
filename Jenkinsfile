pipeline {
 agent none
  
  stages {
   stage('Unit Tests'){
    agent {
     label 'apache'
}
    steps{
     sh 'ant -f test.xml -v'
     junit 'reports/result.xml'
}
}
    stage('build') {
      agent {
       label 'apache'
}
      steps {
        sh 'ant -f build.xml -v'
      }
      post {
       success {
        archiveArtifacts artifacts: 'dist/*.jar', fingerprint:true
}
}

    }
    stage('deploy') {
      agent {
       label 'apache'
}
      steps{
        sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
	sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
}

}    
    stage("Running On Centos"){
     agent{
      label 'Centos'
}
     steps {
      sh "wget http://satishdasi5.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
      sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"  
}
}
    stage("Test on Debian"){
     agent{
      docker 'openjdk:8u151-jre'
}
     steps{
      sh "wget http://satishdasi5.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
      sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
}
}   
    stage('Promote to Green'){
     agent{
      label 'apache'
}
     when{
	branch 'master'
}
     steps{
      sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
}
}
    stage('Promote Devlopment branch to master'){
     agent{
      label 'apache'
}
     when{
      branch 'development'
}
     steps{

	echo"Stashing any local changes"
        sh 'git stash'
        echo "Checking the development branch"
        sh 'git checkout development'
        echo "checking out the master branch"
        sh 'git checkout master'
        echo "Merging development into master"
        sh 'git merge development'
        echo "Pushing the Origin Master"
        sh 'git push origin master'
}  
} 
}
}
