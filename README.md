# docker-php
php+nginx+mysql+redis+其它工具环境

- 先fork项目代码(https://github.com/zsping1989/docker-php.git)到自己的git仓库

- 下载代码自己仓库中的项目代码(这里以我的为例子)
```
cd ~
mkdir code
cd code
git clone https://github.com/自己账户名/docker-php.git

```

- 切换到自己分支进行开发
```
 cd docker-php
 git checkout -b dev

```

- 填写.env配置文件

- 安装docker+docker-compose

- 启动相关服务

```
docker-compose up -d

```


- 进入工具容器进行项目系统安装

```
docker exec -it docker-php_1 bash
```

- 整个项目到此就已经安装完成了,开始开发吧
