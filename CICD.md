# CI/CD

## Runner (docker)

镜像

```
docker pull gitlab/gitlab-runner:latest 
```

创建

```bash
docker run -d --name gitlab-runner --restart always \                                              
     -v /Users/user/gitlab-runner/config:/etc/gitlab-runner \
     -v /var/run/docker.sock:/var/run/docker.sock \
     gitlab/gitlab-runner:latest
```

/Users/user 替换为本机路径

初始化

```bash
docker run --rm -it -v /Users/jxh/gitlab-runner/config:/etc/gitlab-runner gitlab/gitlab-runner register
```

## .gitlab-ci.yml

```yml
# 定义 stages
stages:
  - install
  - build
  - push

.template: &templateDef  # 创建一个锚，'template'
  only:
    - master
    
cache:
  key: ${CI_BUILD_REF_NAME}
  paths:
    - node_modules/
    - dist/
    
# 定义 job
job1:
  stage: install
  <<: *templateDef
  script:
    - npm i

# 定义 job
job2:
  stage: build
  <<: *templateDef
  script:
    - npm run build

job3:
  stage: push
  <<: *templateDef
  script:
    - git config user.name "gitlab"
    - git config user.email "gitlab@email.com"
    - git add ./dist/
    - git commit -m '打包'
    - git push http://username:token@gitlab.com/username/demo.git HEAD:$CI_COMMIT_BRANCH

```

## 发布一个npm包

```yaml
before_script:
  - eval `ssh-agent -s`
  - echo "${SSH_PRIVATE_KEY}" | tr -d '\r' | ssh-add - > /dev/null # add ssh key
  - mkdir -p ~/.ssh
  - chmod 700 ~/.ssh
  - '[[ -f /.dockerenv ]] && echo -e "Host *\n\tStrictHostKeyChecking no\n\n" > ~/.ssh/config'
  - git branch -a
  - git remote set-url origin "ssh://git@gitlab.com/$CI_PROJECT_PATH.git"
  - git config --global user.name "${CI_USERNAME}"
  - git config --global user.email "${CI_EMAIL}"
# 定义 stages
stages:
  - install
  - lint
  - build
  - deploy


cache:
  key: ${CI_BUILD_REF_NAME}
  paths:
    - node_modules/
    
# 定义 job
install:
  stage: install
  retry:
    max: 2
    when:
      - runner_system_failure
      - stuck_or_timeout_failure
  only:
    - master
    - merge_requests
  except:
    variables:
      - $CI_COMMIT_MESSAGE =~ /^chore:\srelease /
  script:
    - npm i

lint:
  stage: lint
  retry:
    max: 2
    when:
      - runner_system_failure
      - stuck_or_timeout_failure
  only:
    - master
    - merge_requests
  except:
    variables:
      - $CI_COMMIT_MESSAGE =~ /^chore:\srelease /
  script:
    - npm run lint

build:
  stage: build
  retry:
    max: 2
    when:
      - runner_system_failure
      - stuck_or_timeout_failure
  only:
    - master
    - merge_requests
  except:
    variables:
      - $CI_COMMIT_MESSAGE =~ /^chore:\srelease /
  script:
    - npm run build

release:
  stage: deploy
  only:
    - master
  except:
    variables:
      - $CI_COMMIT_MESSAGE =~ /^chore:\srelease /
  when: manual
  script:
    - git checkout master
    - git pull origin $CI_BUILD_REF_NAME
    - npm set registry https://registry.npmjs.org
    - npm config set //registry.npmjs.org/:_authToken $NPM_TOKEN
    - npm run release
  artifacts:
    name: "dist"
    when: on_success 
    paths:
      - es
      - lib

build-site:
  stage: deploy
  only:
    - master
  except:
    variables:
      - $CI_COMMIT_MESSAGE =~ /^chore:\srelease /
  when: manual
  script:
    - npm run build-site

```

1. ssh

   1. 生成ssh秘钥
   2. 公钥添加到gitlab用户秘钥中

2. 设置CI环境变量

   1. SSH_PRIVATE_KEY ssh私钥，用于push代码
   2. CI_USERNAME commit需要
   3. CI_EMAIL commit需要
   4. NPM_TOKEN 发布npm包需要
   
3. 发布步骤

   1. npm i 安装依赖
   2. 缓存 node_modules
   3. lint 代码格式校验
   4. build 预编译，防止release时失败
   5. release 生成changelog,升级版本号,编译,发布到npm
      1. git checkout master
      2. git pull
      3. git tag -d vx.x.x // 如果发布失败，仓库已生成了tag，需要手动删除下
      4. 设置要发布的npm （仅私有npm需要设置）
      5. 设置npm的authToken
      6. npm release
   6. 将release的产物保存（artifacts）

4. CI配置

   1. before_script 设置ssh秘钥，git信息
   2. stage 流水线阶段
   3. jobs 阶段下具体任务
   4. 设置触发条件
      1. only
      2. except
   5. when 设置何时触发
   6. artifacts产物

   