# [iOS] App Clips 탐구하기

WWDC 2020에서 발표된 iOS의 App Clips는 게임회사를 비롯한 많은 회사에서 유저에게 앱을 체험할 수 있는 도구로 사용되고 있다.

App clips는 가장 간단하게 설명하면 주요 기능을 탑재한 작은 크기의 원래 앱의 축소화된 앱이다. WWDC 2020에서는 또 다른 앱의 진입점이라고 설명하기도 했다. App clips를 이용하고 진짜 원래 앱을 받아서 이용할 수 있기 때문이다.

## Real world Example
실제 App Clips를 활용하여 새로운 유저 경험을 제공하는 앱들을 상당히 다양하다. 그 중 WWDC를 통해 소개된 예시들은 다음과 같다.

위치 정보를 통하여 빵집에 접근하면 App Clips를 통해 빵을 구매하도록 하거나


QR Code를 통해 따로 앱을 설치하지 않고 주차 비용을 결제하거나


또는 타일을 구매하기 전에 미리 벽에 타일을 체험해볼 수 있다.


이 외에도 다양한 게임앱을 비롯 유저 경험을 통한 마케팅의 일환으로 많이 사용되고 있다. 


> Chibi App Clips 체험하기 → 카메라 앱으로 QR Tag 확인!


## How To Utilize

App clips은 Univeral Link와 유사하게 URL로 접근하게 되서 해당 URL을 활용해서 위와 같은 다양한 채널로 App Clips를 invoke 할 수 있다.
다른 점은 사이트에서 apple-app-site-association 파일을 설정한 사이트 URL을 사용하는 방식이 아니라 AppStore Connects를 통해 해당 URL을 설정하여 사용한다. 

위와 같이 App clips URL을 등록만 하면 App Clips Code, NFC tag, QR Code를 비롯 URL 링크를 활용하여 다양한 곳에서 App Clips를 접근하도록 사용가능하다.

App Clips를 활용하는 전체 Flow를 보면 다음과 같다.


## Implementation

1. App Clips Target을 추가한다.


2. Universal Link 연결을 위한 Associated domain을 설정한다 (` appclips:// `형식으로 구성 )

3. 기존 앱 내 코드를 공유하여 사용하기 위한 App Group 설정을 한다. (Optional)

4. App Clips에 필요한 앱 화면을 구성한다. ( UIKit, SwiftUI 모두 가능 - 주로 기존 앱의 Code를 Target 추가를 통해 활용 ) 

5. URL에 따라 처리하기 위해서는 NSUserActivity를 통해 App Clips의 추가적인 URL 설정이 필요하고 다음과 같은 로직을 추가한다.

6. AppStore connect에서 URL 등록한다.

7. App Clips 카드 UI 규격에 맞게 구성한다.

8. 기존 앱과 동일하게 앱 심사 제출하여 통과한 후 배포가 가능한데 용량 제한 등을
올리기 전에 애플에서 제공하는 툴을 활용하여 미리 체크할 수 있다.

## Limitation



App Clips가 원래 앱의 예고편에 해당하는 작은 앱이기에 여러가지 제약사항이 존재한다. 10MB 이하로만 App Clips를 구성해야 하고 

다음과 같은 카드 UI 구성에 있어서의 규격이 존재하며 반드시 지켜서 구성해야 한다.

또한 HealthKit과 같은 개인정보 접근은 제한되고 일정 기간이 지나면 삭제되므로 로컬 DB를 사용하는 것도 무의미하다.

이런 부분을 제외하면 거의 웬만한 앱과 같이 대부분의 API를 활용하여 구성할 수 있다. 물론 용량의 한계로 인해 3rd party 라이브러리를 활용하는 것은 많이 무리가 될 수 있어 애플 자체 framework를 최대한 활용하는 것을 권장한다.



## App Clip In Web

웹에서 링크 눌러서 동작하게 할 때는 apple-app-site-association 파일 방식을 업데이트해서 사용할 수도 있다.

iOS 15에서 발표된 업데이트에서는 다음과 같이 html에 추가하여

웹의 일부로 카드 UI를 보여줄 수 있다.

## App Clips Code

애플에서는 QR Code와 비슷하게 App Clips를 위한 코드도 제공한다. 

다음과 같이 QR Code처럼 등록된 App Clips URL을 활용하여 App Clips를 invoke 할 수 있고 다양한 색상과 NFC 버전 등의 커스텀이 가능하고

애플에서 주는 툴을 사용하면 손쉽게 생성 가능하다.


> ### Reference
> Configure and link your App Clips [WWDC 2020]
> 
> Testing Your App Clip’s Launch Experience [Apple Documentation]
> 
> 
> Explore App Clips [WWDC 2020]
> 
> What's new in App Clips [WWDC 2021]

