---
layout: post
comments: true
title: "[Spring] Spring 프로젝트 배포"
author: Changyoun Seo
date: 2018-01-23 12:00:00 +0900
tags: [Spring, STS, Server, 공부]
---

# 스프링 프로젝트 배포

## 시작
일단 개인 서버 접속

---

## 환경 설정
1. `~ help`
1. `~ list`
1. `~ install jdk`
1. `vi .bash_profile` 내용 추가(3라인)
1. `source .bash_profile` 환경변수 갱신
  - `export | grep JAVA` 로 확인
  - `java -version` 로 확인
  - `export` 는 모든 환경변수를 확인 가능
1. `~ install apache@2.4.29` && 환경변수 추가
  - 아파치 실행 테스트 `apachectl start`, `nc -z localhost 80`
1. `~ install tomcat@8.5.23`
1. `~/apps/tomcat/bin/startup.sh` 실행
1. 디렉토리 작성: 지금은 ~/bulid `-p`를 붙이면 하위까지 다 만듬
1. 아파치 설정 `~/apps/apache/conf/httpd.conf` (수정내용은 교육자료 참고)
1. 톰캣 설정 `~/apps/tomcat/conf/server.xml` (프로젝트 명 수정 - `war` 로 배포할거임)

---

## 프로젝트 설정
1. `pom.xml` 수정 war로 고치고 repo 추가는 옵션(기존에 쓰는거에서 갖고와서 빠름)
1. {project}Application.java 수정 `extend SpringBootServletInitializer` 등등(수정내용은 교육자료 참고)

---

## 배포
1. 이제 `~/build` 에서 `git clone ~~ `
1. `~/build/{project}` 에서 `./mvnw clean package`
1. `~/build/{project}/target` 에서 `mv {projectName}*.war {projectName}.war`
1. Apache shutdown
1. Tomcat shutdown
1. 기존 배포물 삭제 `rm ~/deploy/{projectName}.war`
1. 최신 배포물 복사 `cp {projectName}.war ~/deploy/`
1. Tomcat startup
1. Apache startup

---

## 로그
- tomcat log 확인
  ```
  $ cd ~/logs/tomcat/apache-tomcat-8.5.23
  $ tail -200f catalina.2018-01-23.log
  ```

- apache log 확인
  ```
  cd ~/logs/apache/apache-2.4.29/
  ```

---

## 마치며
서버에 적용할 떄 참고하면 될 것 같다. 배포는 스크립트로 짜두면 편리
