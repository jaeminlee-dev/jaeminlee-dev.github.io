---
layout: single
title: "flutter in-app-purchase"
categories:
  - 개발
tags:
  - flutter
  - 인앱 구매
  - android
  - ios
  - 출시
---

# 결제 모듈 연결

결제 모듈을 연결할 일이 생겼다.

[https://velog.io/@soonmuu/Flutter-flutter-인앱결제-구현-1-앱빌드](https://velog.io/@soonmuu/Flutter-flutter-%EC%9D%B8%EC%95%B1%EA%B2%B0%EC%A0%9C-%EA%B5%AC%ED%98%84-1-%EC%95%B1%EB%B9%8C%EB%93%9C)

이거를 주로 참고해서 제작했는데 절차는 다음과 같다.

### Using library (flutter_in_app_purchase)

### 안드로이드

1. 구글 플레이스토어에 등록할 abb 파일 생성
2. 구글 플레이 콘솔 앱 등록 및 내부 테스트 추가
3. 테스터 이메일 등록 및 콘솔(라이센스) 추가 → 무료 테스트 기기 등록
4. 결제 양식 작성 및 상품 추가
5. 코드에 결제 상품 코드 맞춰서 검색하고 결제 진행하는 로직 연결

### IOS

1. 앱스토어 connect에 아카이브 후 앱 빌드 배포
2. **App Store Connect에서 앱 내 구입 등록**
3. **유료앱 계약 필요**
   [https://eunjin3786.tistory.com/406](https://eunjin3786.tistory.com/406) 하단 링크 참고
4. **샌드박스 아이디 생성으로 결제 테스트**

### 생각해야 할 것

- 상품의 유형은 consumable, nonConsumable, subscription이 대표적이니 이것에 맞춰 코드 작성
