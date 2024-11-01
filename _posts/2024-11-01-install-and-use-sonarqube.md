---
layout: post
title: 安装并使用SonarQube进行代码质量检测
date: 2024-11-01 00:22
author: admin
comments: true
categories: [SonarQube]
tags: [SonarQube]
---



为提升代码质量，实现代码自动检测，引入SonarQube平台。接入该平台，可以实现对代码进行检测。代码质量检测结果应纳入为流水线强制指标。

<!-- more -->


## SonarQube平台



安装SonarQube平台


```yaml
services:
  sonarqube:
    image: sonarqube:community
    hostname: sonarqube
    container_name: sonarqube
    read_only: true
    depends_on:
      db:
        condition: service_healthy
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
      - sonarqube_temp:/opt/sonarqube/temp
    ports:
      - "9000:9000"
  db:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready"]
      interval: 10s
      timeout: 5s
      retries: 5
    hostname: postgresql
    container_name: postgresql
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
      POSTGRES_DB: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

volumes:
  sonarqube_data:
  sonarqube_temp:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
```

访问 SonarQube 平台，可以在管理界面查看质量报告。

参考：<https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/installing-sonarqube-from-docker/>

## SonarLint


### IntelliJ IDEA接入

针对后端开发者，SonarLint用于本地IntelliJ IDEA扫描。


在线安装方式可能比较慢，也可选择利离线安装。

* 安装方式1：在IntelliJ IDEA 的`Settings > Plugins`搜“SonarLint”进行安装。安装完成后重启IDE生效。
* 安装方式2：在FTP获得离线安装包`sonarlint-intellij-x.x.x.x.zip`，进行离线安装安装。


进入IntelliJ IDEA 的`Settings > Plugins > Tools > SonarLint`，进行配置：


* 取消“Automatically trigger analysis”
* Connectins to SonarQube 接入自建的 SonarQube 平台。

在IntelliJ IDEA中，右键文件或者目录执行SonarLint分析。 


### Visual Studio Code接入

针对前端开发者，SonarLint用于本地Visual Studio Code扫描。


点击Visual Studio Code左侧工具栏的“EXTENSIONS”，搜索“SonarLint”并安装插件。

插件安装完成后，点击Visual Studio Code顶部工具栏的`View > Open View`，并点击“SonarLint”，此时，在左侧工具栏看到“SonarLint”图标。点击“SonarLint”图标，在“CONNECTED MODE”面板点击“Add SonarQube Connection”，填入以下几个选项来完成接入 SonarQube 平台：

* Sever URL
* User Token
* 取消勾选“Receive notifications from SonarQube”。

参考：<https://docs.sonarsource.com/sonarlint/vs-code/>

## Maven插件sonar-maven-plugin



增加SonarQube服务器地址了和token。在pom文件中指定对应的参数，在properties字段提供sonar.host.url和sonar.login等字段的值：

```xml
<properties>
  ...
  <sonar.host.url>http://192.168.1.86:9000</sonar.host.url>
  <sonar.login>sqa_f495da6ee6f26600d0a528cc429c6955c5a1f56d</sonar.login>
  <sonar.sources>src/main</sonar.sources>
  ...
<properties>
```


如果检查是否通过检测，则执行：

```
mvn verify sonar:sonar -Dsonar.qualitygate.wait=true
```

参考：<https://docs.sonarsource.com/sonarqube/latest/analyzing-source-code/analysis-parameters/>


## 流水线集成SonarQube

可以流水线中集成了SonarQube，每次提交代码自动扫描。修改`.gitlab-ci.yml`：

```
mvn-test:
  stage: mvn-test # 按事先定义好的阶段来指定
  tags: # 按产品名称来打tag
    - themetis-engine
  variables:
    SONAR_USER_HOME: "${CI_PROJECT_DIR}/.sonar"
  cache:
    key: "${CI_JOB_NAME}"
    paths:
      - .sonar/cache
  script:
    - echo "Start mvn sonar:sonar... "
    - mvn verify sonar:sonar -Dsonar.qualitygate.wait=true
    - echo "End mvn sonar:sonar."
  allow_failure: false
```



参考：<https://docs.sonarsource.com/sonarqube/latest/devops-platform-integration/gitlab-integration/adding-analysis-to-gitlab-ci-cd/>
