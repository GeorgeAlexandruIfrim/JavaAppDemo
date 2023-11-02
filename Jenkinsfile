pipeline{
    
    agent any
    
    stages{
        stage('Checkout from SCM'){
            steps{
                git changelog: false, 
                poll: false, 
                url: 'https://github.com/GeorgeAlexandruIfrim/JavaAppDemo.git'
            }

        }
        
        stage('Build'){
            steps{
                script{
                  bat 'mvn clean package'
                }
               
            }
        }
        
        stage('SonarQube Scan'){
            steps{
                withSonarQubeEnv('SonarQube'){
                    script{
                     bat 'sonar-scanner -Dsonar.java.binaries=C:/Projects/JavaAppDemo/target/classes/com/georgeifrim/JavaAppDemo'
                    }
                }

            }
        }
        
        stage('Quality Gate') {
            steps {
                //     timeout(time: 2, unit: 'MINUTES') {
                // script {
                //         def qg = waitForQualityGate()
                //         if (qg.status != 'OK') {
                //         error("Pipeline aborted due to Quality Gate failure: ${qg.status}")
                // }
                script{
                   echo 'In Quality gate !' 
                }
            }
        }
    
        stage('Test'){
            steps{
                script{
                    bat 'mvn test'
                }
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                script {
                    def tomcatUrl = "http://localhost:8080/manager/text" 
                    def tomcatUsername = "tomcatadmin"           
                    def tomcatPassword = "password"             
                    def warFile = "C:/ProgramData/Jenkins/.jenkins/workspace/Tema1/target//JavaAppDemo-0.0.1-SNAPSHOT.war"           
        
                    def credentials = "${tomcatUsername}:${tomcatPassword}"
                    def encodedCredentials = credentials.bytes.encodeBase64().toString()

                    bat script: """
                        curl -v --fail -T $warFile $tomcatUrl -H 'Authorization: $encodedCredentials'
                    """, returnStatus: true
        
                    if (currentBuild.resultIsBetterOrEqualTo('SUCCESS')) {
                        currentBuild.result = 'SUCCESS'
                    } else {
                        error "Failed to deploy to Tomcat"
                      }
        
                }
            }
        }

    }    
}
