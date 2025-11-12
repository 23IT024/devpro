pipeline {
    agent any

    tools {
        maven 'M3'
    }

    stages {
        // --- STAGE 1: GET THE CODE ---
        stage('Checkout') {
            steps {
                echo 'Pulling code from devpro.git...'
                // This part is now correct
                git branch: 'main', url: 'https://github.com/23IT024/devpro.git'
            }
        }

        // --- STAGE 2: BUILD THE .WAR FILE ---
        stage('Build & Test') {
            steps {
                // *** THIS IS THE FIX ***
                // Use the 'dir' step to change into the sub-folder
                dir('TomcatMavenApp') {
                    echo "Running 'mvn clean package' inside TomcatMavenApp/"
                    sh 'mvn clean package'
                }
            }
        }

        // --- STAGE 3: DEPLOY TO TOMCAT ---
        stage('Deploy') {
            steps {
                echo 'Deploying to Tomcat server...'
                
                deploy adapters: [tomcat8(
                    credentialsId: 'tomcat-credentials', 
                    url: 'http://localhost:8081', 
                    path: '/manager/text'
                )],
                
                // *** THIS PATH IS ALSO FIXED ***
                // The .war file is now inside the sub-folder's target
                war: 'TomcatMavenApp/target/*.war', 
                
                contextPath: 'devpro' 
            }
        }
    }

    // --- AFTER THE BUILD ---
    post {
        success {
            echo 'Build and Deploy successful!'
            echo '-------------------------------------'
            echo 'To display your index.html, go to:'
            echo 'http://YOUR_SERVER_IP:8081/devpro/index.html'
            echo '-------------------------------------'
            
            // *** THESE PATHS ARE ALSO FIXED ***
            junit allowEmptyResults: true, testResults: 'TomcatMavenApp/target/surefire-reports/*.xml'
            archiveArtifacts artifacts: 'TomcatMavenApp/target/*.war'
        }
    }
}
