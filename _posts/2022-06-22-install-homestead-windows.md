---
title: windows 10 에서 homestead 설치 + laravel 프로젝트 생성
author: roger park
date: 2022-06-22
categories: [Laravel, Homestead]
tags: [laravel, homestead, vagrant, git, virtualbox]
pin: true
---

laravel 을 본격적으로 배우기에 앞서 로컬에서 개발환경을 간단하게
구축할수 있는 Homestead를 설치하여야 한다. valet 이란 것도 있지만
이건 mac 에서만 작동가능하기 때문에 Homestead를 선택했다.
아래는 설치 과정과 과정중에 부딪힌 문제점들과 그 해결법을 정리한 것이다.

## 1. [`VirtualBox`](https://www.virtualbox.org/wiki/Downloads) 와 [`Vagrant`](https://www.vagrantup.com/downloads) 설치
## 2. Vagrant Box 설치
cmd 창을 열고 아래 명령을 실행.
```
vagrant box add laravel/homestead
```
실행하면 provider 선택항이 나온다.
```
1) parallels
2) virtualbox
3) vmware_desktop
```
나는 virtualbox 를 사용하니 2+enter.

## 3. Homestead 설치

### 로컬에 Homestead 저장소 복제하기
cmd 창을 열면 최초의 경로가 `C:\Users\사용자명` 이런 식으로 되어 있는데
바로 아래 명령을 실행하여 homestead 관련파일을 다운로드 한다.
```
git clone https://github.com/laravel/homestead.git Homestead
```
실행하면 `C:\Users\사용자명\Homestead` 폴더가 생성되고 폴더속에 관련
소스들이 들어있다.

master 브랜치보다는 release 브랜치가 안정적이므로 브랜치 체인지.
```
git checkout release
```

### Homestead 설정
이어 Homestead 폴더속 `Homestead.yaml` 파일을 수정해야 하는데
갓 내려받은 소스에는 `Homestead.yaml.example` 만 있었다.
아래 처럼 실행하면 `Homestead.yaml` 파일이 생성된다.
```
# Git Bash
$ cd ~/Homestead && bash init.sh

# Windows Command Prompt
\> cd %HOMEPATH%\Homestead
\> init
```
`Homestead.yaml` 파일 포맷은 검색하면 다 나오니 굳이 상세하게 설명하지 않는다.

### hosts 파일 설정
`Homestead.yaml` 파일중 ip는 예를 들어 `192.168.56.56` 이고,
`sites` 항목의 map에 해당하는 도메인이 `homestead.test` 라고 한다면
`hosts` 파일에 아래와 같이 추가한다.
```
192.168.56.56 homestead.test
```

### ssh key 생성
git bash 에서 아래 처럼 실행시켜주면 된다.
```
# Git Bash

$ mkdir ~/.ssh
$ ssh-keygen -t rsa
# Enter file in which to save the key (/c/Users/suchc/.ssh/id_rsa): <Enter>
# Enter passphrase (empty for no passphrase): <Enter>
# Enter same passphrase again: <Enter>
```

### Homestead 실행, 중지
```
vagrant up                 # vagrant box 구동

vagrant reload --provision # (설정 변경 후 vagrant 다시 실행시)

vagrant ssh                # ssh를 통해 홈스테드 환경에 접속

vagrant halt               # homestead VM 을 중지

vagrant suspend            # 완전 끌 때
```

## 4. Laravel 프로젝트 생성
Homestead 환경에 접속된 상태에서 `Homestead.yaml` 파일중
`forders => to` 항목의 값에 해당하는 폴더에 진입(예: /home/vagrant/code).
그리고 laravel 생성
```
cd /home/vagrant/code
laravel new project1
```
실행하면 `/home/vagrant/code` 에 `project1` 이 생성된다.

### laravel 버전을 지정하여 생성하기
버전을 지정해 주려면 `composer` 이 필요하므로
먼저 설치해준다. Homestead 실행 중인 환경에서
```
composer install
```
설치가 되면 아래 처럼.
```
composer create-project  --prefer-dist  laravel/laravel [프로젝트 이름] [버전]
composer create-project  --prefer-dist  laravel/laravel laravel-5.4 v5.4.0   # 대략 이런 식으로
```
설치중에 에러가 뜨면 php 버전 문제를 의심해봐야 한다.
예를 들면 `laravel 5.4.0` 버전은 `php 8.1` 에선 작동하지 않는다.
이럴 땐 php 버전을 낮춰줘야 한다.
먼저 `Homestead.yaml` 파일중에서 버전을 지정해준다.
```
sites:
    - map: homestead.test
      to: /home/vagrant/code/test/public
      php: '7.2'   # 버전을 지정해줌
```
그리고 Homestead 에 내재되어 있는 버전관리 툴 `update-alternatives`
를 이용하여 php 버전을 바꿔준다.
```
update-alternatives --display php # 查看所有 php 版本和当前版本

update-alternatives --config php # 执行后，会列出当前 php 所有版本和编号，输入编号，切换到执行的版本
```
위의 명령들을 이용하여 어떤 버전으로 업뎃 가능한지 살펴본후
만약 7.2 버전으로 바꾸고 싶다면 `php72` 을 실행해주면 된다.
<br>
그리고 나서 다시 laravel 버전을 지정하여 프로젝트를 생성해주면 된다.
`homestead.test` 에 접속해보니 v2rayN 을 사용한 탓에
`127.0.0.1:10809` 로 호출하는 문제가 발생했다.
아래 처럼 `v2rayN` 에서 직접 호출하는 도메인에 해당 도메인을 추가해주면 된다.
![v2rayN-direct]({{site.url}}/assets/img/blog/v2ray_direct.PNG)
<br>
<br>
그리고 나서 다시 접속하면 아래와 같이 정상적으로 laravel 페이지가 뜬다.
![laravel-5.4-home]({{site.url}}/assets/img/blog/laravel-5-4.PNG)
<br>
<br>
# -끝-












