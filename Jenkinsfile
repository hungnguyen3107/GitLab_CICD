pipeline {
    agent {
        label 'dev-server'
    }
    environment {
        appUser = "shoeshop"
        appName = "shoe-ShoppingCart"
        appVersion = "0.0.1-SNAPSHOT"
        appType = "jar"
        processName = "${appName}-${appVersion}.${appType}"
        folderDeploy = "/datas/${appUser}"
        buildScript = "mvn clean install -DskipTests=true"
        copyScript = "sudo cp target/${processName} ${folderDeploy}"
        permsScript = "sudo chown -R ${appUser}:${appUser} ${folderDeploy}"
        findPidScript = "ps -ef | grep ${processName} | grep -v grep | awk '{print \$2}'"
        killScript = "sudo kill -9"
        runScript = "sudo su ${appUser} -c 'cd ${folderDeploy}; java -jar ${processName} > nohup.out 2>&1 &'"
    }
    stages {
        stage('build') {
            steps {
                sh(script: """ ${buildScript} """, label: 'build with maven')
            }
        }
        stage('deploy') {
            steps {
                sh(script: """ ${copyScript} """, label: "copy the .jar file into deploy folder")
                sh(script: """ ${permsScript} """, label: "set permission folder")
                
                // Tìm PID của quá trình đang chạy và dừng nó nếu tìm thấy
                script {
                    def pid = sh(script: "${findPidScript}", returnStdout: true).trim()
                    if (pid) {
                        sh(script: "${killScript} ${pid}", label: "terminate the running process")
                    } else {
                        echo "No running process found for ${processName}"
                    }
                }
                
                sh(script: """ ${runScript} """, label: "run the project")
            }
        }
    }
}
