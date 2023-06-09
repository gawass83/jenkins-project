# jenkins-project

```
Login into the Jenkins server and follow the below given steps:

Install Plugins:

1. Go to Manage Jenkins.
2. Click on Manage Plugins.
3. Under Available, search for SSH Build Agents plugin and select it.
4. Now search for Pipeline plugin and select it.
5. Now install these plugins.
6. Once installed click on Restart Jenkins when installation is complete and no jobs are running.


Add Credentials:

1. Go to Manage Jenkins.
2. Click on Manage Credentials.
3. Click on (global) under Domains.
4. From the options on the left side, click on Add Credentials.
5. Enter bob under Username.
6. Enter caleston123 under Password.

7. Leave other options as it is and click on OK.


Add Nodes:

1. Go to Manage Jenkins.
2. Click on Manage Nodes and Clouds.
3. From the options available on the left side, click on New Node.

4. Enter the Node name i.e dev.

5. Enable Permanent Agent option and click on OK.

6. Enter /opt under Remote root directory.

7. Enter dev under Labels.

8. Select Launch Agents via SSH under Launch method.

9. Enter gotest-dev01 under Host and select the credentials you created.

10. Under Host Key Verification Strategy, select Non verifying verification strategy.

11. Leave all other options as it is and click on Save

12. Click on the dev node and there shouldn't be any errors.

13. Just in case the dev node is in error state, then try to relaunch it.

14. Follow same steps for adding prod node, just take care about the node name, label and host.


Create and Configure the job:

1. On the left of the Jenkins dashboard, click on New Item.

2. Enter the job name go-app-deployment.

3. Select Pipeline job and click on OK.

4. Under Pipeline section keep selected Pipeline script as Definition and add below given code in the Script

pipeline {
    agent none
    stages {
        //DEV
        stage('Build Dev') {
            agent {
              label {
                label 'dev'
                customWorkspace "/opt/go-app"
              }
            }
            steps {
                sh 'git pull'
            }
        }
        stage('Test Dev') {
            agent {
              label {
                label 'dev'
                customWorkspace "/opt/go-app"
              }
            }
            steps {
                sh 'go test ./...'
            }
        }
        stage('Deploy Dev') {
            agent {
              label {
                label 'dev'
                customWorkspace "/opt/go-app"
              }
            }
            steps {
              script {
                withEnv ( ['JENKINS_NODE_COOKIE=do_not_kill'] ) {
                  sh 'go run main.go &'
                  }
                }
            }
        }
        //PROD
        stage('Build Prod') {
            agent {
              label {
                label 'prod'
                customWorkspace "/opt/go-app"
              }
            }
            steps {
                sh 'git pull'
            }
        }
        stage('Test Prod') {
            agent {
              label {
                label 'prod'
                customWorkspace "/opt/go-app"
              }
            }
            steps {
                sh 'go test ./...'
            }
        }
        stage('Deploy Prod') {
            agent {
              label {
                label 'prod'
                customWorkspace "/opt/go-app"
              }
            }
            steps {
              script {
                withEnv ( ['JENKINS_NODE_COOKIE=do_not_kill'] ) {
                  sh 'go run main.go &'
                  }
                }
            }
        }
    }
}


5. Finally save the job and build.

```
