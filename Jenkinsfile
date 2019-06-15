#!groovy 	 

node { 
  Environment Variables 
  env.gbpass = "${gbadmin}" 
 	env.instance_id = "${instance_id}" 
 	echo "${env.instance_id}" 
 	env.instance_ip = "${instance_ip}" 
 	echo "${env.instance_ip}" 
 	    
  withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                    credentialsId: 'green_berets', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']])
  withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'WindowsOpenSSH_Creds',
                    usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) 
  println(env.USERNAME)
  { 
 	    stage('Cloning Git') {
 	      git 'https://github.com/ticmagicians/gbweb'
      }
 	
      stage('EC2 de-tag TG') {
 	      sh "aws elbv2 deregister-targets --target-group-arn arn:aws:elasticloadbalancing:ca-central-1:228804139688:targetgroup/GB-TIC-TG1/02385ddf6ca63bf7 --region ca-central-1 --targets Id=${env.instance_id}"
 	    }
 	
      stage('Code Deploy') {
 	      echo " Code Deploy" 
 	      sh "sshpass -p ${env.gbpass} scp -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -r /var/lib/jenkins/workspace/Web-Blue-Green-Deploy/default.html gbadmin@${env.instance_ip}:/c:/inetpub/wwwroot/gbweb"
      }
 	
      stage('QA Sign-off') {
 	      timeout(time:5, unit:'DAYS') {
 	        input message:'QA Sign-off ?'
 	      }
 	    }
 	
      stage('EC2 add to TG') {
 	      sh "aws elbv2 register-targets --target-group-arn arn:aws:elasticloadbalancing:ca-central-1:228804139688:targetgroup/GB-TIC-TG1/02385ddf6ca63bf7 --region ca-central-1 --targets Id=${env.instance_id}"
 	    }
 	  }
}
 
