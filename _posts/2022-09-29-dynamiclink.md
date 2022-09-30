---
layout: single
title:  "Dynamic link & deep link"
categories:
  - 개발 정보
tags:
  - flutter
  - Dynamic link
  - Deep link
  - Firebase
  - kakao
---

요약하면 공유기능이다.

이런 카카오톡 공유하기나, url을 생성한뒤에 해당 링크 클릭하면 앱으로 이동하는 기술이다.

아래 동작을 요기요를 써보면서 어떻게 구동하는지 정리해보자

1. 공유하기 클릭 
2. 카카오톡 URL 복사 중 택 1
3. 카카오 클릭시 - 카카오톡으로 이동(카카오 API가 필요하겠지)
4. URL복사
5. 해당 URL 클릭시 선택 문구 나옴(요기요 같은 경우에는 웹으로 이동 후 앱 있는지 확인, 앱 업데이트 필요한 경우에 앱 스토어로 이동)
6. 앱 실행

![Untitled](Dynamic%20link%20&%20deep%20link%2041652d0e7e4b4bbba1085ad55ed372f5/Untitled.png)

![Untitled](Dynamic%20link%20&%20deep%20link%2041652d0e7e4b4bbba1085ad55ed372f5/Untitled%201.png)

![Untitled](Dynamic%20link%20&%20deep%20link%2041652d0e7e4b4bbba1085ad55ed372f5/Untitled%202.png)

![Untitled](Dynamic%20link%20&%20deep%20link%2041652d0e7e4b4bbba1085ad55ed372f5/Untitled%203.png)

이다.

이를 Flutter로 한번 구현해보자. 

그 전에 Dynamic link 와 deep link의 차이점을 알아보자

dynamic link : 우리가 페이스북이나 인스타 등 SNS의 피드를 내리다보면 중간중간 광고가 삽입되어 나온다. 이 광고를 클릭하면 어딘가로 연결이 되는데, 만약 광고한 앱이 안깔려 있다면 깔 수 있도록 앱스토어나 구글스토어로 연결 되고, 깔려 있다면 앱을 실행 시킨다.

deep link : 앱을 실행시켰을 때, 이 앱의 화면이 어디로 연결되어야 할 지를 알려주는 path 값이다. 예를 들어 ‘/main’ 이런식으로 path가 넘어오면 메인화면을 보여주고, ‘/profile’ 이렇게 Path가 넘어오면 프로필 화면을 처음에 띄워준다. dynamic link 를 앱에 붙일 때, deep link 경로를 작성하는 란이 있기 때문에 활용할 수 있다.

즉 지금 만들것은 공유기능이기에. 2개 다 구현해야한다.

 Firebase에 다이나믹 링크를 지원하는 것을 이용해서 url 공유 기능을 만들어보자

[https://arcanine33.github.io/flutter/Flutter-Firebase-Dynamic-Links/](https://arcanine33.github.io/flutter/Flutter-Firebase-Dynamic-Links/)

카카오 공유하기는 이걸로 참고해서 만들어보자

[https://eunjin3786.tistory.com/288](https://eunjin3786.tistory.com/288)

Firebase 동적 링크 플로 차트

IOS

![Untitled](Dynamic%20link%20&%20deep%20link%2041652d0e7e4b4bbba1085ad55ed372f5/Untitled%204.png)

Android

![Untitled](Dynamic%20link%20&%20deep%20link%2041652d0e7e4b4bbba1085ad55ed372f5/Untitled%205.png)

Dynamic Links를 방법은 여러가지가 있다. Firebase에선 아래 4자기 방법으로 만들 수 있다고 소개하고 있다.

1. [Firebase 콘솔](https://console.firebase.google.com/project/_/durablelinks/links/) 사용: 소셜 미디어에서 공유할 프로모션 링크를 만드는 경우에 유용합니다. 이렇게 하면 [Firebase Console](https://console.firebase.google.com/project/_/durablelinks/links/)에서 링크의 이름과 커스텀 서픽스를 선택할 수 있습니다. [Firebase Console](https://console.firebase.google.com/project/_/durablelinks/links/)에서 또는 [Analytics REST API](https://firebase.google.com/docs/reference/dynamic-links/analytics)를 통해 동적 링크의 실적을 추적할 수 있습니다.
2. [iOS](https://firebase.google.com/docs/dynamic-links/ios/create) [Android](https://firebase.google.com/docs/dynamic-links/android/create) 및 [Flutter](https://firebase.google.com/docs/dynamic-links/flutter/create)의 Dynamic Link Builder API 사용. 링크가 여러 개 필요한 경우나 사용자 간의 공유를 위해 앱에서 동적으로 링크를 만드는 데 적합한 방법입니다. Dynamic Links [Analytics API](https://firebase.google.com/docs/reference/dynamic-links/analytics)를 사용해 Builder API로 만든 동적 링크의 실적을 추적할 수 있습니다.
3. [REST API](https://firebase.google.com/docs/dynamic-links/rest) 사용: Builder API가 없는 플랫폼에서 동적으로 링크를 만드는 데 적합한 방법입니다. Console에서 만든 프로모션 캠페인의 실적을 추적하는 데 [Analytics REST API](https://firebase.google.com/docs/reference/dynamic-links/analytics)를 사용할 수 있습니다.
4. [직접 만들기](https://firebase.google.com/docs/dynamic-links/create-manually): 클릭 데이터를 추적할 필요가 없고 링크가 길어도 무방하다면, URL 매개변수를 사용해 직접 동적 링크를 만들 수 있습니다. 직접 만드는 경우에는 추가 네트워크 왕복을 피할 수 있습니다.

2번 동적 링크 생성 파라미터 코드 해설

| --- | --- |

| --- | --- |

| --- | --- |

| --- | --- |

| --- | --- |

| --- | --- |

| --- | --- |

---

## [Kakao 공유 공식 문서](https://developers.kakao.com/docs/latest/ko/message/flutter-link)

기존에 link → share로 카카오 공유 문서가 바뀌었다. 함수도 다르니  공식 문서 참조

공식 문서를 참고해서 

```dart
void shareMyKakaoCode(String code) async {
    bool isKakaoTalkSharingAvailable = await ShareClient.instance.isKakaoTalkSharingAvailable();
    var dynamicLink = await DeepLinkKakao.getkakaoDynamicLink(code);
    var template = DeepLinkKakao.getTemplate(dynamicLink, code);

    if (isKakaoTalkSharingAvailable) {
      try {
        Uri uri = await ShareClient.instance.shareDefault(template: template);
        await ShareClient.instance.launchKakaoTalk(uri);
        print('카카오톡 공유 완료');
      } catch (error) {
        print('카카오톡 공유 실패 $error');
      }
    } else {
      try {
        Uri shareUrl = await WebSharerClient.instance.makeDefaultUrl(template: template);
        await launchBrowserTab(shareUrl, popupOpen: true);
      } catch (error) {
        print('카카오톡 공유 실패 $error');
      }
    }
  }
```

를 만들었는데(잘못된 작성)

E/flutter
 ( 9354): [ERROR:flutter/runtime/dart_vm_initializer.cc
(41)] Unhandled Exception: LateInitializationError: Field 'hosts' has not been initialized.

가 발생했었다.

따로 검색해도 안나와서 hosts 관련된 문서를 찾아보니 Kakao Init을 해야함을 알았다.

(기존 Link라이브러리 때는 필요 없었는데 share로 바뀌면서 필요한 것 같다.)

```dart
KakaoSdk.init()
```

main에 KakaoSdk.init(네이티브 앱 키, 자바스크립트 앱 키)을 넣어주고 디버깅을 하면서 잘못 해석했던 것들을 수정했다.

```dart
void shareMyKakaoCode(String code) async {
    String link = await DeepLinkService.getShareLink("event");
    final TextTemplate defaultText = TextTemplate(
      text:
          '메모장 어플에 어서오세요',
      link: Link(
        webUrl: Uri.parse(link),
        mobileWebUrl: Uri.parse(link),
      ),
    );
    bool isKakaoTalkSharingAvailable = await ShareClient.instance.isKakaoTalkSharingAvailable();

    if (isKakaoTalkSharingAvailable) {
      try {
        Uri uri = await ShareClient.instance.shareDefault(template: defaultText);
        await ShareClient.instance.launchKakaoTalk(uri);
        print('카카오톡 공유 완료');
      } catch (error) {
        print('카카오톡 공유 실패 $error');
      }
    } else {
      try {
        Uri shareUrl = await WebSharerClient.instance.makeDefaultUrl(template: defaultText);
        await launchBrowserTab(shareUrl, popupOpen: true);
      } catch (error) {
        print('카카오톡 공유 실패 $error');
      }
    }
  }
```

이 상태에서 키 해시 등록도 필요해서 등록했다. [등록 방법](https://jaejong.tistory.com/169)

코드를 해석해보면 기존 Firebase dynamic link 서비스 함수로 event link를 만들고

카카오 공유 템플릿에 해당 코드를 넣어줬다.

카카오 공유가 가능한 상태(앱 설치 여부)를 확인 되면

uri를 생성 uri란 [https://velog.io/@syoung125/개념공부-URI는-무엇이고-URL은-무엇일까](https://velog.io/@syoung125/%EA%B0%9C%EB%85%90%EA%B3%B5%EB%B6%80-URI%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9D%B4%EA%B3%A0-URL%EC%9D%80-%EB%AC%B4%EC%97%87%EC%9D%BC%EA%B9%8C)

후

uri를 기준으로 카카오톡 공유 기능을 수행한다.

앱 설치가 안되어 있으면 wep url를 열어서 공유하기를 실행한다.

왼쪽 앱 설치 안되어 있을 시

오른쪽 앱 설치 되어 있을시

![스크린샷 2022-09-30 18.33.23.png](Dynamic%20link%20&%20deep%20link%2041652d0e7e4b4bbba1085ad55ed372f5/%25E1%2584%2589%25E1%2585%25B3%25E1%2584%258F%25E1%2585%25B3%25E1%2584%2585%25E1%2585%25B5%25E1%2586%25AB%25E1%2584%2589%25E1%2585%25A3%25E1%2586%25BA_2022-09-30_18.33.23.png)

![KakaoTalk_Image_2022-09-30-18-35-56.jpeg](Dynamic%20link%20&%20deep%20link%2041652d0e7e4b4bbba1085ad55ed372f5/KakaoTalk_Image_2022-09-30-18-35-56.jpeg)

따로 카카오 url를 생성하는 것이 아닌 구글 dynamic link에 uri를 만들어서 카카오톡 연결 후 보내준다고 생각하면 이해가 편하다. 그러므로 기존의 링크 클래스를 안건들여도 이벤트에 따라 페이지 이동도 그대로 동작한다!

++추가로 카카오 템플릿이 많은데 하단 링크를 참조해서 원하는 템플릿을 만들어보자..!

[Kakao Developers](https://developers.kakao.com/docs/latest/ko/message/flutter-link#create-message)