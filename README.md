<p align="center"><img src="https://www.discuz.net/static/image/common/logo.svg"></p>

# Discuz! Q 云开发二次开发指引

## 介绍

```shell
.
├── Dockerfile # 默认镜像声明文件
├── Dockerfile.centos
├── README.md
├── cloudbaserc.json # 云开发部署声明文件
├── docker-compose.yml
├── download.sh # 下载代码脚本
├── root
│   ├── etc
│   │   ├── crontab
│   │   ├── nginx
│   │   ├── php
│   │   ├── supervisord.conf
│   │   └── supervisord_nginx.conf
│   ├── usr
│   │   └── sbin
│   │       ├── init_dzq.sh
│   │       ├── run_sched.sh
│   │       ├── start_cron.sh
│   │       └── supervisord
│   └── var
│       └── www
│           ├── discuz
│           └── temp
└── run # 构建镜像脚本
```

## 下载代码

[构建/发布PC端 参考文档](https://discuz.com/docs/web_dev.html#%E8%AF%B4%E6%98%8E)
[构建/发布小程序与H5前端](https://discuz.com/docs/uniapp_hbuilderx.html#%E4%B8%8D%E4%BD%BF%E7%94%A8hbuilder%E6%9E%84%E5%BB%BA)

当前 Discuz! Q 尚未开源，只放出了代码包，可通过该代码包进行二次开发。
1、[pc端最新下载地址](https://dl.discuz.chat/discuz_web_latest.zip)
2、[小程序与H5前端 最新下载地址](https://dl.discuz.chat/uniapp_latest.zip)
3、[Discuz! Q代码下载地址](https://dl.discuz.chat/dzq_latest_install.zip)，包括了 后台源代码，与构建好的前端代码，前端代码在 public 目录下。

下面演示如何下载源代码

```shell
# 创建source目录
mkdir -p source
cd source
# 下载pc端代码
curl https://dl.discuz.chat/discuz_web_latest.zip -o discuz_web_latest.zip
unzip discuz_web_latest.zip
# 下载h5代码
curl https://dl.discuz.chat/uniapp_latest.zip -o uniapp_latest.zip
unzip uniapp_latest.zip

# 下载并解压 DQ 后台程序代码
curl https://dl.discuz.chat/dzq_latest_install.zip -o dzq_latest_install.zip
unzip dzq_latest_install.zip -d dzq_latest_install
```

## 二次开发

### 1、启动服务

1、通过`docker-compose up`。将默认启动后端代码并且挂载本地的源码目录 `source/dzq_latest_install`，并且创建一个本地mysql数据库，方便本地开发。
2、可在 `source/discuz_web_v2.1.201126` 目录执行 `yarn install && yarn dev` 运行web端服务
3、可在 `source/uniapp_v2.1.201126` 目录执行 `yarn install && yarn dev:h5` 运行h5端服务

### 2、配置代理到本地开发

本地开发前端代码时，后台请求默认代理到DQ提供的公共服务 `https://dq.comsenz-service.com` 上，如果希望改变代理。请修改如下配置

1、`source/uniapp_v2.1.201126/vue.config.js`文件夹 `devServer.proxy['/api'].target`
2、`source/discuz_web_v2.1.201126/config.js`文件夹内的 `DEV_API_URL`

### 3、构建镜像

#### 步骤一

配置云开发镜像库

[查询云开发环境的docker镜像相关账号、镜像名称等信息](https://console.cloud.tencent.com/tcb/service/detail?envId=dzqtcb001-4gi7e7vbb8f09&rid=4&tab=image&name=discuzq)
并设置 `run` 脚本里的`USERNAME` `IMAGE_NAME` 为正确值

#### 步骤二

执行 `run` 脚本等候将镜像推送到镜像库(期间可能会要求输入镜像仓库密码)

### 更新服务

### 方式1

登录云开发控制台，在云托管处点击[原版本编辑配置并重新部署](https://docs.cloudbase.net/run/update-service.html#fang-shi-er-yuan-ban-ben-bian-ji-pei-zhi-bing-chong-xin-bu-shu)。

### 方式1

安装云开发命令行工具 `npm i -g @cloudbase/cli`。

更新 `cloudbaserc.json` 中的 `imageUrl` 为构建出的最新的镜像地址，并执行 `tcb` 部署到对应的环境中。
