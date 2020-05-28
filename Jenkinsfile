pipeline {
   agent any
   // 环境变量 
   environment {
       GIT_USER_NAME = 'jenkinsCI'
       GIT_USER_EMAIL = '****@***.com'
       GIT_ADDRESS = 'git@github.com:jinxuanzheng01/blog-xcx-ci-demo.git'
       BRANCH_NAME = 'master'
   }
   stages {
        // 拉取git代码
        stage('git pull') {
            steps {
                git branch: "${BRANCH_NAME}", credentialsId: '1', url: 'git@github.com:jinxuanzheng01/blog-xcx-ci-demo.git'
            }
        }
        // 询问当前版本信息
        stage('inquirer version') {
            steps {
                script { 
                    // 读取版本信息
                    def versionJson = readJSON file: './version.config.json', text: ''
                    
                    // 设置问题描述
                    def userInput = input(
                        id: 'versionInput',
                        message: '请设置版本信息', 
                        parameters: [
                            [defaultValue: versionJson.version, description: '设置版本号', name: 'VERSION', $class: 'TextParameterDefinition'],
                            [defaultValue: 'jenkins CI is upload trial version as: ' + new Date().format('yyyy-MM-dd HH:mm:ss'), description: '设置版本描述(please use english)', name: 'VERSIONDESC', $class: 'TextParameterDefinition']
                        ])
        			
                    // 设置全局变量
                    env.VERSION = userInput.VERSION;
                    env.VERSIONDESC = userInput.VERSIONDESC;
                    
                    // 重写本地版本文件（为后续进行版本提交做准备）
                    writeJSON file: './version.config.json', json: [version:  env.VERSION, versionDesc: env.VERSIONDESC], pretty: 4;
                }
            }
        }
        // 构建
        // stage('build') {
        //     steps {
        //         nodejs('NodeJS 14.3.0') {
        //             sh "npm install"
        //             sh "npm run build"
        //         }
                
        //     }
        // }
       
        // 推送版本信息到git仓库
        stage('push version2git') {
            steps {
                sh "git config --local user.name ${GIT_USER_NAME} && git config --local user.email ${GIT_USER_EMAIL}"
                sh "git add version.config.json"
                sh "git commit -m 'docs: 更改版本号为${VERSION}'"
                sh "git push origin ${BRANCH_NAME}"
            }
        } 
   }
}