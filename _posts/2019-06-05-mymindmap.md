---
layout: post
title: "아키텍쳐 자바_01"
comments: true
---

아키텍쳐 :
프로젝트에 참여하는 개발자들이 설계에 대해 공유하는 이해를 반영하는 주관적인 개념  
  - 중요한 것  
  - 변경하기 어려운 것  
  - 가급적이면, 일찍, 올바르게 바꾸고 싶은 것  

레이어 아키텍쳐 : 유사한 관심사들을 레이어로 나눠서 수직적으로 배열 (간단하게 케익 같은 구조.)
  - 유사한 얘들은 한 레이어에 만들어두었으니 그 레이어를 빼서 다른 곳으로 바꾸는 것이 가능.
  - 프리젠테이션 레이어 : 화면 조작, 사용자의 입력을 처리하기 위한 관심사를 처리함.
  - 도메인 레이어 : 도메인적인 내용을 묶어서 처리
  - 데이타소스 레이어 : 데이터를 관리하기 위한 부분들을 전부 묶어서 처리