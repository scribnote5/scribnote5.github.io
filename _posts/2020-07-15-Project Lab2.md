---
title: "Project Lab 2. Windows Subsytem for Linux(WSL)에 mariaDB 설치"
excerpt: "Windows Subsytem for Linux(WSL)에 mariaDB 설치 과정을 소개한다."
categories:
  - Web
  - Project Lab
last_modified_at: 2022-06-27
layout: post
---
- Windows Subsytem for Linux(WSL)에 mariaDB 설치 과정을 소개한다.
- 본 프로젝트에서는 추후 우분투 서버 환경에서 배포할 예정이다. 따라서 mariaDB의 명령어를 공부하고 익숙해지기 위해서, Windows Subsytem for Linux(WSL)에 mariaDB를 설치하였다.
- Windows Subsytem for Linux(WSL)란 리눅스에서 제공되는 프로그램들을 윈도우 환경에서 사용할 수 있도록 bash 쉘을 지원한다. 이를 통해서 별도의 가상머신 없이 리눅스를 사용할 수 있다.
- 만약 해당 과정이 번거로운 경우, mariaDB를 윈도우 설치해서 사용해도 상관 없다. 사용자 편의에 따라서 선택하여 설치하면 된다.
- 22.06.27 기준 WSL과 mariaDB는 서로 호환이 안되는지 mariaDB 서비스가 실행되지 않으며, 여러 해결 방법을 찾아보고 적용하였으나 해결하지 못하였다. 



## Window 10에 우분투 bash 쉘 설치
- 하단 출처를 참고하여 Window에 우분투 bash 쉘을 설치한다.

출처: <https://harryp.tistory.com/730>



## mariaDB 설치
- 'Ubuntu' 앱을 실행한다.

```bash
$ sudo apt update -y
$ sudo apt upgrade -y

$ sudo apt install mariadb-server mariadb-client -y

# mysql 서비스 시작
# 참고로 mysql 서비스를 자동 시작하지 않는 이상, WSL을 실행할 때 마다 수시로 서비스를 시작해야 한다.
$ sudo service mysql start
```



## mariaDB 보안 설정
```bash
# mysql 보안 설정
$ sudo mysql_secure_installation
```

- 하단 그림과 같이 mysql 보안 설정에 필요한 내용에 그림과 같이 입력하면 보안 설정이 완료된다.

![image](/assets/img/2020-05-02-Project Lab2/image1.png)

```bash
$ Enter current password for root (enter or none)
- OS의 root 계정의 비밀번호를 입력한다. root 권한으로 실행하였기 때문에 엔터키를 입력하여 넘어간다.

$ Set root password? [Y/n]
- Y: MariaDB의 root 계정의 비밀번호를 설정한다

$ Remove anonymous users? [Y/n]
- Y: 익명 사용자를 제거한다.

$ Disallow root login remotely? [Y/n]
- n: 원격 접속을 허용한다.

$ Remove test database and access to it? [Y/n]
- Y: 테스트 DB를 생성하지 않는다.

$ Reload privilege tables now? [Y/n]
- Y: 권한 테이블을 reload 하여 지금까지 입력한 내용을 반영한다.
```



## mariaDB 새로운 계정을 외부에서 접속하도록 허용하기
- mariaDB를 DB 관리 도구인 DBeaver에서 접속 가능하도록 설정한다.

```bash
# mariaDB root 계정 접속
$ sudo mysql -u root -p

# 새로운 계정 생성
# CREATE USER '<User>'@'%' IDENTIFIED BY '<Password>';
$ CREATE USER 'scribnote5'@'%' IDENTIFIED BY '123123123';

# 계정에 모든 권한 부여(모든 외부 IP에서 접근 가능하도록 설정)
# GRANT ALL PRIVILEGES ON *.* TO '<User>'@'%' IDENTIFIED BY '<Password>';
$ GRANT ALL PRIVILEGES ON *.* TO 'scribnote5'@'%' IDENTIFIED BY '123123123';

# 생성된 계정 조회 후 확인
$ SELECT host, user, password FROM mysql.user;

# lab DB 생성
$ CREATE DATABASE lab;
```



## mariaDB 계정 삭제
- 계정 및 DB 삭제 명령어다.

```bash
# 계정 삭제
$ DROP USER 'scribnote5'@'%';


# 생성된 계정 조회 후 확인
$ SELECT host, user, password FROM mysql.user;

# 테스트 DB 제거
$ DROP DATABASE lab;
```

출처: <https://jimnong.tistory.com/744><br>
<https://zetawiki.com/wiki/MySQL_%EC%9B%90%EA%B2%A9_%EC%A0%91%EC%86%8D_%ED%97%88%EC%9A%A9>