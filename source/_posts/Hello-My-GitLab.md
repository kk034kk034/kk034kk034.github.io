---
title: Hello, My GitLab
date: 2024-10-13 17:20:33
categories:
	- build
tags:
	- VCS
---
# 新創必備 - 在 Raspberry Pi 上部署 GitLab CE
- Build Own VCS, GitLab
- Community Edition(CE) is Free
- Deploy on Raspberry Pi is Easy and Cost Down
- gitlab.rb is Flexibility

# Quick Start
安裝方式分成兩種，一種是直接安裝，另一種是docker版(建議)。
我是樹莓派4B，所以是安裝arm64v8的版本。
## 直接安裝
[下載deb檔案](https://packages.gitlab.com/gitlab/raspberry-pi2)
```bash
$ sudo dpkg -i gitlab-ce_8.13.0-ce.0_armhf.deb
$ sudo gitlab-ctl reconfigure
```
初始默認的port是80，若有需要改port，就修改/etc/gitlab/gitlab.rb裡面的external_url及其nginx['listen_port']，例如想改成8080。
```bash
$ sudo vi /etc/gitlab/gitlab.rb
i
external_url 'http://your-ip:8080'
nginx['listen_port'] = 8080
:qw
$ sudo gitlab-ctl reconfigure
```

## docker版安裝
[GitLab-ce docker image builder for arm64v8](https://registry.hub.docker.com/r/yrzr/gitlab-ce-arm64v8)
```bash
$ sudo docker pull yrzr/gitlab-ce-arm64v8:latest
```
```bash
docker run \
  --detach \
  --restart unless-stopped \
  --name gitlab-ce \
  --privileged \
  --memory 8G \
  --publish 22:22 \
  --publish 80:80 \
  --publish 443:443 \
  --hostname gitlab.example.com \
  --env GITLAB_ROOT_PASSWORD="YourPasswordHere" \
  --env GITLAB_OMNIBUS_CONFIG=" \
    registry['enable'] = false; \
    GITLAB_OMNIBUS[your_other_configs] = `options`; "\
  --volume /srv/gitlab-ce/conf:/etc/gitlab:z \
  --volume /srv/gitlab-ce/logs:/var/log/gitlab:z \
  --volume /srv/gitlab-ce/data:/var/opt/gitlab:z \
  yrzr/gitlab-ce-arm64v8:latest
```
或我建議是用docker-compose.yml做，因為未來可能還有其他服務可以寫在一起管
```bash
$ vi docker-compose.yml
```
```bash
version: '3'
services:
  gitlab:
    image: yrzr/gitlab-ce-arm64v8:latest
    container_name: gitlab
    restart: always
    privileged: true
    environment:
      TZ: 'Asia/Taipei'
      GITLAB_OMNIBUS_CONFIG: |
        external_url = "http://your-ip"
        gitlab_rails['time_zone'] = 'Asia/Taipei'
#        gitlab_rails['gitlab_ssh_host'] = 'your-ip'
#        gitlab_rails['gitlab_shell_ssh_port'] = 2222
    ports:
      - '80:80'
      - '443:443'
#      - '2222:22'
    volumes:
      - '/home/pi/docker-gitlab/config:/etc/gitlab'
      - '/home/pi/docker-gitlab/logs:/var/log/gitlab'
      - '/home/pi/docker-gitlab/data:/var/opt/gitlab'
    logging:
      driver: "json-file"
      options:
        max-size: "20m"
        max-file: "10"
```
```bash
$ sudo docker-compose up -d
```
我大概等了十分鐘能連上 http://your-ip:80 ，初次啟動的帳戶是root，密碼要到/etc/gitlab/initial_root_password看，然後用這個密碼登入，之後就可以改密碼了。
```bash
$ sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
```
其他指令也都是要藉由docker exec進去執行，例如
```bash
$ sudo docker exec -it gitlab gitlab-ctl status
```

