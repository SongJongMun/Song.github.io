---
layout: post
title:  "2017. 01. 09 Eror Issue"
date:   2017-01-09 17:40:48 +0900
categories: Toast Rookie 4th Study
---

### NHN 엔터테인먼트 루키달님TF 송종문 ###

Today Error Issue

1. STS, Oracle Mysql, JDK 설치 후 STS 실행 시 Exit Code = 13, 1 발생
2. Maven Install, Update 시 Spring Webmvc Jar 파일과 Mysql-connector 관련 jar 파일에서 Invaild END header 발생 
3. Github blog Upload시 작성한 포스트와 수정내용이 적용되지 않음


Error 해결 방법

1. JDK javax.exe 파일 참조 시 Oracle JDK Javax와 Java1.8 Javax 파일이 충돌, STS에서는 Oracle JDK Javax 파일을 VM으로 인식하여 알수 없는 오류 및 실행이 되지 않음 -> Spring.ini 파일 첫 줄에 -clean을 통해 Oracle Javax 설정을 지우고 Java Javax파일을 -vm을 통해 명시함으로서 해결
2. 관련 Jar 파일을 잘못 받아서 생긴 문제, 사용자 폴더\.m2\repository 파일에 존재하는 해당 jar 파일을 제거하고 다시 install 하여 해결
3. Github Repository의 Code Commit 상황 확인, relative_permalinks 환경이 문제가 되어 블로그에 적용되지 못함을 확인하고 해당 코드를 삭제하여 해결함

