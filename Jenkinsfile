pipeline {
    agent any

    tools {
         // 지정된 이름으로 Gradle을 사용하도록 설정
        gradle 'gradle8.2.1'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Git checkout step with Git tool named "Default"
                    checkout([
                        $class: 'GitSCM',  // GitSCM 클래스를 사용하여 Git 관련 설정을 정의합니다.
                        branches: [[name: '*/master']],  // 가져올 브랜치를 '*/master'로 지정합니다.
                        doGenerateSubmoduleConfigurations: false,  // 서브모듈 구성을 자동으로 생성하지 않도록 설정합니다.
                        extensions: [],  // Git 확장 옵션을 지정하지 않습니다.
                        submoduleCfg: [],  // 서브모듈 구성도 지정하지 않습니다.
                        userRemoteConfigs: [  // Git 원격 저장소 설정을 정의합니다.
                            [url: 'https://github.com/o4p1f5/IntelliJ-test-2.git']  // 원격 저장소 URL을 지정합니다.
                        ]
                    ])
                }
            }
        }

        stage('Prepare Gradle Wrapper') {
            steps {
                // 빌드할 수 있도록 gradlew 파일에 권한 부여
                sh 'chmod +x ./gradlew'
            }
        }

        stage('Build') {
            steps {
                // Gradle clean and build
                sh './gradlew clean build'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                // Copy the WAR file to Tomcat server using scp with SSH key authentication
                // 기존 ROOT.war, ROOT.old 파일은 지우고 ROOT 파일을 ROOT.old로 변경
                // scp 명령어를 통해 10.0.3.10(tomcat 서버)로 war파일 배포(여기선 hello-spring-0.0.1-SNAPSHOT.war)
                // hello-spring-0.0.1-SNAPSHOT.war 파일을 ROOT라는 이름으로 변경

                sh '''
                ssh root@10.0.1.10 "rm -rf /usr/local/src/tomcat/webapps/ROOT.war"
                ssh root@10.0.1.10 "rm -rf /usr/local/src/tomcat/webapps/ROOT.old"
                ssh root@10.0.1.10 "mv /usr/local/src/tomcat/webapps/ROOT /usr/local/src/tomcat/webapps/ROOT.old"
                scp build/libs/test2-0.0.1-SNAPSHOT.war root@10.0.1.10:/usr/local/src/tomcat/webapps
                ssh root@10.0.1.10 "mv /usr/local/src/tomcat/webapps/test2-0.0.1-SNAPSHOT/ /usr/local/src/tomcat/webapps/ROOT/"
                
                '''

                // 배포가 완료되면 로그에 메시지 출력
                echo 'WAR 파일을 원격 Tomcat 서버로 배포했습니다...'
            }
        }

    }
}
