#!groovy

node {
    stage('Checkout') {
        checkout scm
    }
    
    stage('Setup Selenium Grid') {
        sh 'docker pull elgalu/selenium && docker pull dosel/zalenium'
        sh 'docker run --rm -d --name zalenium -p 4444:4444 \
            -v /var/run/docker.sock:/var/run/docker.sock \
            -v /tmp/videos:/home/seluser/videos \
            --privileged dosel/zalenium start'
    }
    stage('Run tests') {
        try {
            withMaven(maven: 'maven3') {
                dir('SeleniumTest') {
                    sh 'mvn clean test -Dwebdriver.type=remote -Dwebdriver.url=http://localhost:4444/wd/hub -Dwebdriver.cap.browserName=chrome -Dmaven.test.failure.ignore=true'
                }
            }
        } finally {
            junit testResults: 'SeleniumTest/target/*.xml', allowEmptyResults: true
            archiveArtifacts 'SeleniumTest/target/**'
        }
    }
}
