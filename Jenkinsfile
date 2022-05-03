node{

   stage('SCM Checkout'){

     git 'https://github.com/rmarimut/my-app.git'

   }

   stage('Compile-Package'){

     def mvnHome =  tool name: 'maven3', type: 'maven'  

      sh "${mvnHome}/bin/mvn clean package"

                  sh 'mv target/myweb*.war target/newapp.war'

   }
    stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven3', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
		
   stage('Build Docker Imager'){

   sh 'docker build -t rmarim/myweb:0.0.2 .'

   }

   stage('Docker Image Push'){

   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {

   sh "docker login -u rmarim -p ${dockerPassword}"

    }

   sh 'docker push rmarim/myweb:0.0.2'

   }
     stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 52.66.248.181:8083"
   sh "docker tag rmarim/myweb:0.0.2 52.66.248.181:8083/ranji:1.0.0"
   sh 'docker push 52.66.248.181:8083/ranji:1.0.0'
   }

   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  Do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest rmarim/myweb:0.0.2' 
   }
}
}
