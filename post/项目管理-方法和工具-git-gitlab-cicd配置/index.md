# Gitlab-CICD配置


## 1. .gitlab-ci.yaml详解

```yml
stages:  # 定义构建阶段，非必须
  - build 

job1: #任务说明
  script: 
    - execute-script-for-job1  # 直接执行系统命令
  stage: build  # 指定在哪个阶段,不能使用-
  tags:
    - docker # 指定runner的标签
  artifacts:  # 指定附件
    untracked: true  # 发送所有没有被git跟踪的文件
    name: ""  # 指定name，"$CI_JOB_NAME"当前job的名字"$CI_COMMIT_REF_NAME"当前分支名称或者是tag${CI_JOB_STAGE}当前stage
    when: on_failure  # on_success - 当job成功的时候上传artifacts。默认值on_failure - 当job失败的时候上传artifactsalways - 不论job失败还是成功都上传artifacts
    expire_in: "8h"  #用于过期后删除邮件上传的artifacts。默认情况下，artifacts都是在GitLab中永久保存
    paths:
      - binaries/
      - .config
```

## 2. 使用标记

### 2.1. [badges/shields](https://github.com/badges/shields)

1. Concise, consistent, and legible badges in SVG and raster format

### 2.2. [sebbo2002/gitlab-badges](https://github.com/sebbo2002/gitlab-badges)

1. This server acts like a very tiny shields.io to generate SVG badges for your private GitLab instance. Uses shield.io's gh-badges to generate them.

### 2.3. [gitlab-ci-using-badges-for-each-job](https://stackoverflow.com/questions/45756371/gitlab-ci-using-badges-for-each-job)
