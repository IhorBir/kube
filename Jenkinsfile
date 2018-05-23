1node {
    
    stage('git clone') {
        checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/spring-projects/spring-boot.git']]])
        //sh "docker run --rm --name build_jar -v $(pwd):/usr/src/mymaven/spring-boot-samples/spring-boot-sample-actuator -w /usr/src/mymaven maven:3.5.3-jdk-8 mvn package"
    }
    
    stage('Test') {
        dir('spring-boot-samples/spring-boot-sample-actuator') {
            mvnhome = tool 'maven'
            sh "'${mvnhome}/bin/mvn' test"
        }
    }
    
    stage('Build') {
        dir('spring-boot-samples/spring-boot-sample-actuator') {
            mvnhome = tool 'maven'
            sh "'${mvnhome}/bin/mvn' package -DskipTests"
        }
    }
    
    stage('Build to container') {
        dir('spring-boot-samples/spring-boot-sample-actuator') {
            dir('docker') {
                git changelog: false, poll: false, url: 'https://github.com/IhorBir/kube.git'
            }
            sh "docker build -t gcr.io/sonic-cumulus-205009/${JOB_NAME}:$BUILD_NUMBER -f docker/Dockerfile ."
        }
    }
    
    stage('Push to google registry') {
        sh "gcloud docker -- push gcr.io/sonic-cumulus-205009/${JOB_NAME}:$BUILD_NUMBER"
    }

}
