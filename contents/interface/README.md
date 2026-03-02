---
title: "인터페이스 프로그램 개발"
sort: 8
---

인터페이스 프로그램 개발

Salesforce와 인터페이스를 하는 프로그램을 개발할 때 사용자 Id와 암호를 사용하는 방식이 아닌 RSA 암호화 인증서 및 공개키를 사용하여 access token을 얻는 JWT Bearer 플로우 방식이 좀 더 보안상 안전하며, 이를 적용하기 위해서는 인증서를 발급받고, Connected App을 설정을 하여야 합니다.

## [Connected App 설정](./connectedapp/)

OAuth2와 JWT 인증 방식을 사용하기 위하여 Salesfoece에서 Connected App을 설정합니다.

## [Java 프로그램 개발](./java/)

OAuth2와 JWT 인증 방식을 사용하는 Java 프로그램의 개발 방법입니다.

## [Python 프로그램 개발](./python/)

OAuth2와 JWT 인증 방식을 사용하는 Python 프로그램의 개발 방법입니다.
