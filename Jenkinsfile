 pipeline {
    agent any
    
	environment {
	    jenkinsPort = "8080"
	    dockerPort = "8081"
        dockerName = "sights"
		apiRepo = "git@github.com:sax0224/KeelungSights.git"
// 		apiRepo = "git@github.com:sax0224/tag1.git"
// 		apiRepo = "git@github.com:sax0224/tag2.git"
		testCaseRepo = "git@github.com:sax0224/sights_test.git"
// 		testCaseRepo = "git@github.com:sax0224/tag1.git"
// 		testCaseRepo = "git@github.com:sax0224/tag2.git"
        mail = "sax0224@gmail.com"
        jobName = 'currentBuild.fullDisplayName   ' 

	}      
        stage('Run API') {
            steps {
                script {
                    dir('api') {
                        try {
                            sh "mvn -Dmaven.test.failure.ignore=false clean package"
                            sh "java -jar target/demo-0.0.1-SNAPSHOT.jar"
                        }
                        catch (exc) {
                            //stopDocker()
                            error "Run docker fail."
                        }
                    }
                }
            }
        }      
    }
}
