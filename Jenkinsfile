pipeline {
    agent any
  tools{
    maven 'Maven'
  }
  stages {
      stage ('Initialize') {
        steps {
            sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
      
     /*  stage ('Check-Git-Secrets') {
            steps {
                  sh 'rm trufflehog || true'
                  sh 'docker run gesellix/trufflehog --json https://github.com/saleem024/webapp.git > trufflehog'
                  sh 'cat trufflehog'
      }
    } 
       stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/saleem024/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    } 
     stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar'
          //sh 'cat target/sonar/report-task.txt'
        }
      }
    }
      
         stage ('Build') {
               steps {
                       sh 'mvn clean package'
                 }
          }
       stage ('Deploy-To-Tomcat') {
            steps {
                sshagent(['tomcat']) {
                         sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@54.234.14.90:/prod/apache-tomcat-9.0.65/webapps/webapp.war'
                   }      
             }       
          }*/
      
      stage ('Port Scan') {
		    steps {
		       	sh 'rm nmap* || true'
		       	sh 'docker run --rm -v "$(pwd)":/data uzyexe/nmap -sS -sV -oX nmap 65.61.137.117' // 54.234.14.90'
			       sh 'cat nmap'
		    }
	        }
      
       stage ('DAST') {
            steps {
                sshagent(['zap']) {
                    sh 'ssh -o  StrictHostKeyChecking=no ubuntu@18.205.233.12 "docker run -t owasp/zap2docker-stable zap-baseline.py -t https://demo.testfire.net/" || true' //http://54.234.14.90:8080/webapp/" || true'
                    }
            }
       }
           stage ('Nikto Scan') {
		    steps {
			sh 'rm nikto-output.xml || true'
			sh 'docker run --user $(id -u):$(id -g) --rm -v $(pwd):/report -i secfigo/nikto:latest -h 65.61.137.117 -p 8080 -output /report/nikto-output.xml'
			sh 'cat nikto-output.xml'   
		   }
	    }
	stage ('SSL Checks') {
		    steps {
		        sh 'docker run --rm -i nablac0d3/sslyze:5.0.0  65.61.137.117:8443 --json_out=results.json || true'
		    }
	    }
    }
}
	    
