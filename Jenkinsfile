pipeline {
    agent any

    tools {
        // This tells Jenkins to use the 'M3' Maven we just set up
        maven 'M3'
    }

    stages {
        // --- STAGE 1: GET THE CODE ---
        stage('Checkout') {
            steps {
                echo 'Pulling code from devpro.git...'
                git branch: 'main', url: 'https://github.com/23IT024/devpro.git'
            }
        }

        // --- STAGE 2: BUILD THE .WAR FILE ---
        stage('Build & Test') {
            steps {
                echo 'Running mvn clean package...'
                // This command builds your app
                sh 'mvn clean package'
            }
        }

        // --- STAGE 3: DEPLOY TO TOMCAT ---
        stage('Deploy') {
            steps {
                echo 'Deploying to Tomcat server...'

                // This uses the plugin and credentials we just set up
                deploy adapters: [tomcat8(
                    // This ID must match the credentials you created in Jenkins
                    credentialsId: 'tomcat-credentials', 

                    // This is your Tomcat URL (on the new port)
                    url: 'http://localhost:8081', 

                    path: '/manager/text'
                )],
                // This is the file that Maven just built
                war: 'target/*.war', 

                // This will be the name of your app
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

            // This saves your .war file in Jenkins
            archiveArtifacts artifacts: 'target/*.war'
        }
    }
}
