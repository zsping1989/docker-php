#!groovy

node() {
  def TAG_NAME = "${BRANCH_NAME}.b${env.BUILD_ID}";
  def BACKEND_IMAGE = "zsping1989/docker-php:${TAG_NAME}";

  try {
    //更新代码
    stage('Checkout') {
      checkout scm
    }

    //检查代码
    stage('Checkout Code') {
      script {
        docker.image('phpqa/phpcs').inside("-v $PWD:/app") {
          sh 'cd /app'
          sh 'phpcs . --extensions=php --ignore=vendor,database,public,node_modules,resources/views --standard=PSR1'
        }
      }
    }

    //安装composer依赖
    stage('Install dependencies') {
      script {
        docker.image('composer:1.6').inside("-v /var/composer-cache:/tmp -v $PWD:/app") {
          sh returnStdout: true, script: 'composer config -g repo.packagist composer https://packagist.phpcomposer.com'
          sh returnStdout: true, script: 'composer install --ignore-platform-reqs --no-scripts --classmap-authoritative'
        }
      }
    }

    // 静态资源编译
    // stage('Frontend build') {
    //   docker.image('node:11').inside("-v $PWD:/app") {
    //     sh 'npm install'
    //     sh 'npm build'
    //   }
    // }

    //编译镜像
    stage('Build docker images') {
      script {
        docker.build(BACKEND_IMAGE, "./docker/php-fpm")
      }
    }

    //测试镜像
    stage('Unit Test') {
        script {
            //数据库环境
            docker.image('mysql:5.7').withRun("--env-file ${WORKSPACE}/.jenkins.env"){ container ->
                docker.image('mysql:5.7').inside("--link ${container.id}:db") {
                    sh "while ! mysql -h db -P 3306 -u root -psecret ; do sleep 3; done"
                }
                docker.image(BACKEND_IMAGE).inside("--link ${container.id}:db") {
                    sh 'cp .env.example .env' //测试环境配置
                    sh 'php artisan key:generate' //生成app_key
                    sh 'php artisan migrate --seed' //系统安装（数据迁移+数据填充）
                    //sh 'php artisan passport:install' //api接口认证安装
                    sh 'wget http://phar.phpunit.cn/phpunit.phar' //phpunit安装
                    sh 'chmod +x phpunit.phar'
                    sh './phpunit.phar' //执行测试
                }
            }
        }
    }

    //发布镜像
    stage('Push docker images') {
      script {
        docker.withRegistry('https://镜像地址.aliyuncs.com', '镜像仓库') {
          docker.image(BACKEND_IMAGE).push() //发布
        }
      }
    }
  } finally {
    // 发送消息通知
    def currentResult = currentBuild.result ?: 'SUCCESS'
    if(currentResult=='SUCCESS'){
       emailext body: '''${SCRIPT, template="groovy-html.template"}''',
                 mimeType: 'text/html',
                 subject: "[Jenkins] ${currentBuild.fullDisplayName}",
                 recipientProviders: [[$class: 'CulpritsRecipientProvider']]
    }else{
     emailext body: '''${SCRIPT, template="groovy-html.template"}''',
                 mimeType: 'text/html',
                 subject: "[Jenkins] ${currentBuild.fullDisplayName} 测试失败",
                 recipientProviders: [[$class: 'CulpritsRecipientProvider']]
    }
  }
}
