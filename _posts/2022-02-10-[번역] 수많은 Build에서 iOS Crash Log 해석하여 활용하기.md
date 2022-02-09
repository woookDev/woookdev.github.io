---
title: "[iOS] 수많은 Build에서 iOS Crash Log 해석하여 활용하기 (.ips 파일 탐구) - 번역"
excerpt: "[iOS] 수많은 Build에서 iOS Crash Log 해석하여 활용하기 (.ips 파일 탐구) - 번역"
toc: true
toc_sticky: true
categories:
  - Programming
tags:
  - iOS
  - Debug
  - CrashLOG

last_modified_at: 2022-02-10T08:06:00-05:00
published: true
---



>해당 글은 [Ferlix Yanto Wang](https://medium.com/@ferlixyantowang) 의 [medium 포스트](https://medium.com/tokopedia-engineering/facilitate-your-ios-crash-log-translation-for-numerous-builds-
>8cbd614fe88b)를 번역한 글입니다. 따라서 해당 글에서 언급하는 필자는 위 medium글의 필자를 의미합니다.

<hr>

<center>
<figure>
<img src="/assets/images/ips_1.png" alt="">
<figcaption></figcaption>
</figure>
</center>

```
왜 앱에서 크래시가 발생하는지 이해하기 위해  Crash Stack 
기록들을 읽고 이해할 수 있는 함수 이름과 코드 라인으로 해석하는 과정
```
([Crash Report 해석에 관한 애플 문서](https://developer.apple.com/documentation/xcode/adding-identifiable-symbol-names-to-a-crash-report))

<hr>

<center>
<figure>
<img src="/assets/images/ips_2.png" alt="">
<figcaption></figcaption>
</figure>
</center>

우리는 모두 문제를 일으키지 않는 앱을 만들기를 소망한다. 하지만 현실은 앱을 개발하는 도중이든 앱이 이미 배포된 이후이든 앱이 크래시를 일으키는 것을 종종 발견하게된다. 발견하게된 크래시는 재현 상황을 파악하기 위해 노력하면서 문제가 되는 코드를 발견하게 된다. 최악의 경우, 재현이 되지 않아 문제 상황 파악이 어려워지고 패닉에 빠지기도 한다.

혹자는 **Crashlytics**와 같은 Third party 툴이 이런 경우를 위해 있으므로 사용하면 되지 않냐고 생각할 수 있다. **Crashlytics**가 이와 같은 경우 역할을 할 수 있다는 점은 일부는 사실이지만 일부는 아니다. 예를 들어 필자는 Tokopedia라는 앱을 Crashlytics를 세팅하여 App Store에 배포하였다. 하지만 **Crashlytics**를 개발환경에서는 세팅하여 사용하고 있지 않는다. 이유는 개발 환경에서는 너무나 많은 빌드가 이뤄지고 **Crashlytics**의 대부분의 기능은 개발 환경에서는 불필요할 수 있기 때문이다. 따라서 필자의 iOS Tokopedia 앱에서 우리는 개발환경에 특화된 Crash Log 해석 서비스를 구축하여 크래시 디버깅 프로세스를 개선하고자 했다.

## Crash Log 해석 시작해보기

iOS 앱에서 크래시가 발생하면 OS에서 암호화된 Crash Log를 담은 파일을 생성한다. 해당 파일은 **.ips** 파일로 불린다. 크래시가 발생한 앱의 디바이스로 부터 아래와 같은 과정을 통해 해당 파일을 얻을 수 있다.

<center>
<figure>
<img src="/assets/images/ips_3.png" alt="">
<figcaption></figcaption>
</figure>
</center>

[애플 문서 링크](https://developer.apple.com/documentation/xcode/acquiring-crash-reports-and-diagnostic-logs#Locate-Crash-Reports-and-Memory-Logs-on-the-Device)

실제 **.ips** 파일의 예시를 살펴보면 아래와 같은 구성이다.

```
Thread 0 name:  Dispatch queue: com.apple.main-thread
Thread 0 Crashed:
0   libswiftCore.dylib            	0x00000001aed1f8ac 0x1aecee000 + 202924
1   libswiftCore.dylib            	0x00000001aed1f8ac 0x1aecee000 + 202924
2   libswiftCore.dylib            	0x00000001aed1ee30 0x1aecee000 + 200240
3   libswiftCore.dylib            	0x00000001aed012c4 0x1aecee000 + 78532
4   libswiftCore.dylib            	0x00000001aed05f18 0x1aecee000 + 98072
5   TokopediaDebug                	0x00000001073c8874 0x102d3c000 + 73975924
6   TokopediaDebug                	0x00000001073cfac4 0x102d3c000 + 74005188
7   UIKitCore                     	0x00000001ad4a1098 0x1ad038000 + 4624536
8   UIKitCore                     	0x00000001ad4a5650 0x1ad038000 + 4642384
9   UIKitCore                     	0x00000001ad4a5a30 0x1ad038000 + 4643376
```

이로써, 좀 더 해당 Crash 이슈를 다루는데 한 걸음 다가가게 되었다. 하지만 해당 파일의 16진법 코드 블럭을 해석하기는 어려운 상태이다. 해당 파일을 읽을 수 있는 상태로 만드는 해석과정이 필요하다. 이러한 **.ips** 파일을 해석하는 일은 두개의 다른 키로 보물 상자를 여는 것과 같다. 또한 해당 보물상자는 정해진 사람만 열 수 있다. 이러한 과정을 시각화해보면 아래와 같다.

<center>
<figure>
<img src="/assets/images/ips_4.png" alt="">
<figcaption></figcaption>
</figure>
</center>

## 1. Device Symbol

**Device Symbol**은 위 Crash Log에서 UIKitCore, libswiftCore.dylib와 같은 OS framework의 정보를 해석하기 위해 사용되는 정보 더미이다.

>0   **libswiftCore.dylib**            	0x00000001aed1f8ac 0x1aecee000 + 202924 <br>
...<br>
7   **UIKitCore**                     	0x00000001ad4a1098 0x1ad038000 + 4624536

**Device Symbol**은 맥에 디바이스를 연결해서 얻을 수 있다. **Device Symbol**은 각 OS와 디바이스의 CPU 아키텍쳐에 특별하게 존재한다. 따라서 iOS 15에서 동작하는 아이폰 디바이스에서 크래시가 발생하면 iOS 15에 해당하는 **Device Symbol**을 사용하여 암호화된 로그를 해석한다. 하나 팁을 공유하자면, 공간을 절약하기 위해 가장 최신 patch의 OS 버전용 **Device Symbol**을 사용하는 것이 좋다. 예를 들어, iOS 15 버전에서 여러번의 patch 업데이트 즉, iOS 15.0.0, iOS 15.0.1 그리고 iOS 15.0.2 가 존재한다고 한다면 그냥 iOS 15.0.2 버전의 **Device Symbol**을 사용하면 그 iOS 15 그 하위 patch 버전에서 발생하는 크래시의 로그를 모두 해석할 수 있다.

## 2. dSYM (Debug Symbol File)

**Debug Symbol File** 줄여서 **dSYM**  파일은 **Device Symbol** 와 다르게 OS의 암호화된 Crash Log를 해석하기 위한 정보가 아니라 개발자의 애플리케이션 framework, 예를 들어 필자의 경우 **TokopediaDebug** 라이브러리 framework의 암호화된 Crash Log 해석을 위해 사용한다.

```
TokopediaDebug                	0x00000001073c8874 0x102d3c000 + 73975924 <br>
TokopediaDebug                	0x00000001073cfac4 0x102d3c000 + 74005188
```

이러한 **dSYM**  파일은 앱스토어에 업로드할 때 만들어지고 다운로드가 가능하다. 또는 개발자가 CI / CD 시스템을 구축한 경우 CI / CD를 활용하여 얻을 수 있다. 또한 이러한 **dSYM**  파일은 업로드된 각 앱 빌드에 유니크한 파일로 존재한다. 따라서 Crash Log 해석을 위해서 모든 빌드의 **dSYM**  파일을 저장해야만 한다.

## 3. Symbolicatecrash

**Symbolicatecrash**은 암호화된 Crash Log 파일 즉, **.ips** 파일을 적합한 **dSYM**  파일과 **Device Symbol**을 활용하여 해석하는 명령어이다. 해당 명령어는 Xcode 내부 다음 경로로 접근할 수 있다.

>Xcode.app/Contents/SharedFrameworks/DVTFoundation.framework/Versions/A/Resources/

필자의 iOS Tokopedia 프로젝트에서는 위 과정을 하나의 bash script로 해당 명령을 구성하여 터미널에서 한 줄의 명령어로 실행할 수 있도록 하였다. 따라서 해당 한 줄의 명령어를 실행하면 마법처럼 아래의 snippet을 볼 수 있다. 이제 Crash Log를 읽을 수 있고 Crash Log 해석을 통해 주요 원인을 파악해나갈 수 있다. 

여기서 중요한 것은 위에서 언급했듯이 **.ips** 파일은 두가지 열쇠로 여는 보물상자라서 두가지 적절한 열쇠 (**dSYM**  파일과 **Device Symbol**) 가 필요하다. 둘 중 하나라도 정확하지 않은 열쇠라고 한다면 부분적으로 Crash Log 해석이 이뤄지거나 이뤄지지 않는다. 예를 들어, 아래는 잘못된 **Device Symbol**을 전달하였을 때 발생한 snippet으로 OS framework에 대한 로그가 제대로 해석이 이뤄지지 않았음을 확인할 수 있다.


```
Thread 0 name:  Dispatch queue: com.apple.main-thread
Thread 0 Crashed:
0   libswiftCore.dylib            	0x00000001aed1f8ac 0x1aecee000 + 202924
1   libswiftCore.dylib            	0x00000001aed1f8ac 0x1aecee000 + 202924
2   libswiftCore.dylib            	0x00000001aed1ee30 0x1aecee000 + 200240
3   libswiftCore.dylib            	0x00000001aed012c4 0x1aecee000 + 78532
4   libswiftCore.dylib            	0x00000001aed05f18 0x1aecee000 + 98072
5   TokopediaDebug                	0x00000001073c8874 HomePageViewController.viewDidLoad+ 73975924 () + 204
6   TokopediaDebug                	0x00000001073cfac4 @objc HomePageViewController.viewDidLoad+ 74005188 () + 32
7   UIKitCore                     	0x00000001ad4a1098 0x1ad038000 + 4624536
8   UIKitCore                     	0x00000001ad4a5650 0x1ad038000 + 4642384
9   UIKitCore                     	0x00000001ad4a5a30 0x1ad038000 + 4643376
```

만약 **dSYM**  파일만 적절하지 않은 경우 아래와 같이 개발자의 framework에 대한 Crash Log 해석이 제대로 이뤄지지 않는 것을 확인할 수 있다.

```
Thread 0 name:  Dispatch queue: com.apple.main-thread
Thread 0 Crashed:
0   libswiftCore.dylib            	0x00000001aed1f8ac closure #1 in closure #1 in closure #1 in _assertionFailure+ 202924 (_:_:file:line:flags:) + 380
1   libswiftCore.dylib            	0x00000001aed1f8ac closure #1 in closure #1 in closure #1 in _assertionFailure+ 202924 (_:_:file:line:flags:) + 380
2   libswiftCore.dylib            	0x00000001aed1ee30 _assertionFailure+ 200240 (_:_:file:line:flags:) + 468
3   libswiftCore.dylib            	0x00000001aed012c4 _ArrayBuffer._checkInoutAndNativeTypeCheckedBounds+ 78532 (_:wasNativeTypeChecked:) + 208
4   libswiftCore.dylib            	0x00000001aed05f18 Array.subscript.getter + 84
5   TokopediaDebug                	0x00000001073c8874 0x102d3c000 + 73975924
6   TokopediaDebug                	0x00000001073cfac4 0x102d3c000 + 74005188
7   UIKitCore                     	0x00000001ad4a1098 -[UIViewController _sendViewDidLoadWithAppearanceProxyObjectTaggingEnabled] + 104
8   UIKitCore                     	0x00000001ad4a5650 -[UIViewController loadViewIfRequired] + 928
9   UIKitCore                     	0x00000001ad4a5a30 -[UIViewController view] + 28
```

물론 두가지 모두 잘못 전달하는 최악의 경우는 전혀 Crash Log 해석이 이뤄지지 않는다.
 
## 추가적으로 살펴볼 부분
 
 이제 디버그 과정을 더욱 수월하게 해줄 수 있는 iOS Crash Log **.ips** 파일을 적절하게 읽을 수 있는 해결법을 알게 되었다. 더 나아가 프로젝트에 적용할 수 있는 방법이 있을까? 필자의 iOS Tokopedia에서는 이러한 전체 Crash Log 해석 프로세스를 개발 환경에서 하나의 서비스로 구성하여 앱 배포 시스템에 연결하고 새로운 빌드가 업로드 되는 경우 자동적으로 **dSYM**  파일이 업로드 되도록 하였다.
 
<center>
<figure>
<img src="/assets/images/ips_5.png" alt="">
<figcaption></figcaption>
</figure>
</center>
 
 
 이러한 시스템을 통해 해당 빌드를 이용하는 사람들이 Crash Log를 업로드 할 수 있도록 하고 결과적으로 해당 **.ips** 파일을 손쉽게 해석하여 원인 파악을 해나갈 수 있도록 구축하였다. 따라서 누군가 개발환경 빌드에서 Crash가 발생하는 것을 발견하면  단지 **.ips** 파일을 해석하는 script를 통해 해석본을 올리고 따로 **dSYM**  파일 업로드를 요청하고 개발자가 처리해야하는 부분이 없이 개발자는 결과적으로 나온 읽을 수 있는 Crash Log를 보고 Crash Log의 Stack을 따라가서 주요 원인을 파악하면 된다. 즉, 원인을 찾기위해 수작업으로 해당 Crash를 재현하는 작업이 불필요할 수 있다. 
 
 필자의 iOS Tokopedia에서는 Symboligator service라고 명명하고 있고 모두 공유는 어렵지만 좀 더 자세히 알아보고 싶은 경우 다음 [링크](https://priorart.ip.com/IPCOM/000267878)를 참조하기 바란다.
 
## 정리하며..
 
 개발 과정에서 Crash가 발생하는 것을 완전히 피하는 것은 어렵다. 하지만 그 원인을 찾아가며 최소화하기 위해 노력하는 것은 가능하다. 어떻게 크래시가 발생하였는지 분명한 정보를 얻는다면 디버깅 과정에 쏟는 시간을 절약할 수 있고 좀 더 원인을 찾는 시간보다 크래시 이슈를 해결하는데 집중할 수 있다. 이러한 연장선으로 더 나아가 필자의 방식처럼 툴을 활용하는 방식 등을 통해 적합한 Crash Log 해석 시스템을 구성할 수도 있을 것이다.
 
 
