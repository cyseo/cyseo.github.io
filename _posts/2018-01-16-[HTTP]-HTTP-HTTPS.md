---
layout: post
comments: true
title: "[HTTP] HTTP, HTTPS의 이해"
author: Changyoun Seo
date: 2018-01-16 12:00:00 +0900
tags: [HTTP, HTTPS, 공부]
---

# HTTP

## 시작하며
- HTTP는 OSI 7 레이어의 Application 계층에서 사용. (Transport 계층에서 사용하는 TCP/IP 위에 있다)
- 보통 프로토콜은 header + payload 부분으로 구성됨
- 구성 : [Ethernet header - IP header - TCP header - **HTTP header**] + [**HTTP payload**]
- Stateless: 이전 상태는 필요 없고 지금 잘해

## World Wide Web
- URL(리소스 위치) + HTTP(리소스 접근) + HTML(리소스 자체)

## 주요 응답 코드
- 301: 사이트가 아예 바뀌어서 리다이렉트 시키는 경우
- 302: 잠깐 바뀐 경우
- 401: 로그인 안한경우
- 403: 로그인은 했으나 권한이 없는 경우 (이렇게 구분하면 용이)

## 쿠키
- set-cookie(+domain) 해서 보내면 client는 해당 도메인으로 접속할때는 쿠키를 같이 보내

---

# HTTPS
- 구성 : [Ethernet header - IP header - TCP header - **SSL/TLS header**] + [**암호화된 HTTP Data(header + body)**]

## 마치며
끗. 스팩을 보고 익히는걸 추천한다.