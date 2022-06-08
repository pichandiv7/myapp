node{
   stage('SCM Checkout'){
     git 'https://github.com/pichandiv7/myapp.git'
   }
   stage('Compile-Package'){

      def mvnHome =  tool name: 'maven7', type: 'maven'   
      sh "${mvnHome}/bin/mvn clean package"
	  sh 'mv target/myweb*.war target/newapp.war'
   }
   stage('SonarQube Analysis') {
	        def mvnHome =  tool name: 'maven7', type: 'maven'
	        withSonarQubeEnv('sonar') { 
	          sh "${mvnHome}/bin/mvn sonar:sonar"
	        }
	    }
   stage('Build Docker Imager'){
   sh 'docker build -t pichandi/myweb:0.0.1 .'
   }
   stage('Docker Image Push'){
   withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
   sh "docker login -u pichandi -p ${dockerPassword}"
    }
   sh 'docker push pichandi/myweb:0.0.1'
   }
   stage('Nexus Image Push'){
   sh "docker login -u admin -p admin123 13.127.179.239:8083"
   sh "docker tag pichandi/myweb:0.0.1 13.127.179.239:8083/pichandi:1.0.0"
   sh 'docker push 13.127.179.239:8083/pichandi:1.0.0'
   }
   stage('Remove Previous Container'){
	try{
		sh 'docker rm -f tomcattest'
	}catch(error){
		//  do nothing if there is an exception
	}
   stage('Docker deployment'){
   sh 'docker run -d -p 8090:8080 --name tomcattest pichandi/myweb:0.0.1' 
   }
}
}
