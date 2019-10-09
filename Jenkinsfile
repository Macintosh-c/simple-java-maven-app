pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    emailext attachLog: true, body: '''<!DOCTYPE html>
                    <html>
                    <head>
                    <meta charset="UTF-8">
                    <title>${PROJECT_NAME}-第${BUILD_NUMBER}次构建日志</title>
                    </head>

                    <body leftmargin="8" marginwidth="0" topmargin="8" marginheight="4"
                        offset="0">
                        <table width="95%" cellpadding="0" cellspacing="0"
                            style="font-size: 11pt; font-family: Tahoma, Arial, Helvetica, sans-serif">
                            <tr>
                                <td>(本邮件是程序自动下发的，请勿回复！)</td>
                            </tr>
                            <tr>
                                <td><br />
                                <b><font color="#0B610B">构建信息($BUILD_STATUS)</font></b>
                                <hr size="2" width="100%" align="center" /></td>
                            </tr>
                            <tr>
                                <td>
                                    <ul>
                                        <li>项目名称 ： ${PROJECT_NAME}</li>
                                        <li>构建编号 ： 第${BUILD_NUMBER}次构建</li>
                                        <li>触发原因 ： ${CAUSE}</li>
                                        <li>构建日志 ： <a href="${BUILD_URL}console">${BUILD_URL}console</a></li>
                                        <li>工作目录 ： <a href="${PROJECT_URL}ws">${PROJECT_URL}ws</a></li>
                                        <li>Allure Report ： <a href="${BUILD_URL}allure">${BUILD_URL}allure</a></li>
                                    </ul>
                                </td>
                            </tr>
                            <tr>
                                <td><b><font color="#0B610B">构建日志(最后100行):</font></b>
                                <hr size="2" width="100%" align="center" /></td>
                            </tr>
                            <tr>
                                <td><textarea cols="80" rows="30" readonly="readonly"
                                        style="font-family: Courier New">${BUILD_LOG, maxLines=100}</textarea>
                                </td>
                            </tr>
                        </table>
                    </body>
                    </html>''', recipientProviders: [developers(), culprits(), brokenBuildSuspects(), brokenTestsSuspects(), upstreamDevelopers(), requestor()], subject: '$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!', to: '657582163@qq.com'
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
