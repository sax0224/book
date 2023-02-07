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
	
    stages {
        stage('Unit test') {
            steps {
                script {
                    dir('api') {
                        try {
                            git branch: 'main', credentialsId: 'github', url: env.apiRepo
                            sh "mvn clean"
                            sh "mvn test"
                        }
                        catch (exc) {
                            error "Unit test fail."
                        }
                    }
                }
            }
        }        
        stage('Check version') {
            steps {
                script {
                    dir('api') {
                        try {
                            devVer = sh(returnStdout:  true, script: "git tag --sort=-creatordate | head -n 1")
                            devVer = devVer.trim()
                            echo "devVer: ${devVer}"
                        }
                        catch (exc) {
                            error "Develop API repository is not exist !!!"
                        }
                    }
                    dir('test_case') {
                        try {
                            git branch: 'main', credentialsId: 'github', url: env.testCaseRepo
                            testVer = sh(returnStdout:  true, script: "git tag --sort=-creatordate | head -n 1")
                            testVer = testVer.trim()
                            echo "testVer: ${testVer}"
                        }
                        catch (exc) {
                            error "Test case repository is not exist !!!"
                        }
                    }
                }
            }
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
        stage('Run test case') {
            steps {
                script {
                    dir('test_case') {
                        if (devVer != testVer){
                            preTestVer = ""
                            def(devMajor, devMinor, devPatch) = devVer.split("\\.")
                            devSemVer = "${devMajor}.${devMinor}"
                            intDevPatch = Integer.parseInt(devPatch)
                            def(testMajor, testMinor, testPatch) = testVer.split("\\.")
                            testSemVer = "${testMajor}.${testMinor}"
                            intTestPatch = Integer.parseInt(testPatch)
                            if (devSemVer == testSemVer && intDevPatch > intTestPatch){
                                try {
                                    sh "git checkout ${testVer}"
                                    sh "mvn -Dmaven.test.failure.ignore=false clean package"
                                }
                                catch (exc) {
                                    //stopDocker()
                                    error "Running test case fail"
                                } 
                                // for (i=intTestPatch; i>=0; i--){
                                //     preTestVer = "${testMajor}.${testMinor}.${i}"
                                //     echo "preTestVer: ${preTestVer}\n"
                                //     try {
                                //         sh "git checkout ${preTestVer}"
                                //         sh "mvn -Dmaven.test.failure.ignore=false clean package"
                                //     }
                                //     catch (exc) {
                                //         //stopDocker()
                                //         error "Running test case fail"
                                //     }                                
                                //     if (i == 0){
                                //         echo "the same"
                                //         break
                                //     }
                                // }
                            } else {
                                error "API version and Test case version are not the same."
                            }

                        }
                    }
                    //stopDocker()
                }
            }
        } 
    }
}
