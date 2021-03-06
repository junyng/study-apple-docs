# Bundle

번들은 파일시스템에서 실행가능한 코드 및 이미지 및 사운드와 같은 관련 자원을 함께 묶은 디렉터리이다. iOS 및 OS X에서 애플리케이션, 프레임워크, 플러그 인, 다른 타입의 소프트웨어는 번들이다. 번들은 실행 코드와 해당 코드에서 사용되는 자원을 보유하는 표준화된 계층 구조를 포함하는 디렉터리이다. Foundation 및 Core Foundation은 번들 내의 코드와 리소스를 로드하고 위치를 지정하는 포함하고 있다.

> **Note**: 애플리케이션은 써드 파티 개발자가 iOS에서 생성할 수 있는 유일한 번들 타입이다.

번들은 사용자와 개발자들에게 여러가지 이점을 가져다 준다. 이는 한 위치에서 다른 위치로 애플리케이션이나 다른 소프트웨어를 쉽게 설치하거나 재배치할 수 있도록 한다. 번들은 또한 중요한 국제화의 요인이다. 지역화된 리소스를 번들의 하위 디렉터리에 저장한다. 프로그램적인 기능으로 사용자의 언어 기본 설정에 해당하는 위치에서 지역화된 리소스를 찾는다.

대부분의 Xcode 프로젝트 타입은 빌드가 가능할 때 번들을 생성한다. 따라서 직접 번들을 구성할 필요가 없다. 그렇더라도 그들의 구조를 이해하는 것과 안에 있던 코드와 리소스에 접근하는 방법이 중요하다.

### Structure and Content of Bundles

번들은 실행가능한 코드, 이미지, 사운드, nib 파일, private 프레임워크 및 라이브러리, 플러그인, 로드 가능한 번들 또는 코드 또는 리소스의 다른 종류를 포함할 수 있다. 또한 정보 프로퍼티 목록\(Info.plist\)이라는 런타임 구성 파일도 포함되어 있다. 이 항목들은 번들 구조에서 적절한 자리를 차지하고 있다. 이미지, 사운드 및 nib 파일과 같은 리소스는 `Resource` 하위 디렉터리에 저장된다. 이는 지역화 또는 비지역화가 가능하다. 지역화된 파일\(지역화된 문자열 모음인 문자열 파일을 포함\) `Iproj`의 확장자 및 언어에 해당하는 이름 및 로케일을 사용할 수 있는 `Resource`의 서브 디렉토리에 놓인다.

![](../../.gitbook/assets/bundle_2x.png)

### Accessing Bundle Resources

각 애플리케이션에는 애플리케이션 코드를 포함하는 번들인 메인 번들이 있다. 사용자가 애플리케이션을 시작할 때 즉시 필요한 코드와 리소스를 메인 번들에서 찾아 메모리에 로드한다. 그 후, 애플리케이션은 필요에 따라 메인 번들 또는 하위 번들에서 동적으로\(그리고 lazily\) 코드와 자원을 로드할 수 있다.

[`NSBundle`](https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/Classes/NSBundle/Description.html#//apple_ref/occ/cl/NSBundle) 클래스와 절차 코드의 경우, Core Foundation의 [`CFBundleRef`](https://developer.apple.com/documentation/corefoundation/cfbundle) 불투명 타입은 애플리케이션에서 리소스 번들을 찾을 수 있는 수단을 제공한다. Objective-C에서 먼저 물리적 번들에 해당하는 `NSBundle` 인스턴스를 얻어라. 애플리케이션의 메인 번들을 얻으려면 클래스 메서드 [`mainBundle`](https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/Classes/NSBundle/Description.html#//apple_ref/occ/clm/NSBundle/mainBundle)을 호출하라. 다른 `NSBundle` 메서드는 파일 이름, 확장명 및 \(선택적으로\) 번들 하위 디렉토리가 지정되었을 때 번들 리소스를 반환하는 경로를 리턴한다. 리소스에 대한 경로가 있으면 해당 클래스를 사용해 메모리에 로드할 수 있다.

### Loadable Bundles

애플리케이션 번들과 마찬가지로 로드 가능한 번들 패키지 실행 코드 및 관련 리소스가 있지만 런타임에 번들을 명시적으로 로드한다. 로드 가능한 번들을 사용하여 고도로 모듈화되고 사용자 정의 가능하며 확장 가능한 애플리케이션을 설계할 수 있다. 모든 로드가능한 번들에는 번들의 시작점인 _principal class_가 있다. 번들을 로드할 때 [`NSBundle`](https://developer.apple.com/library/archive/documentation/LegacyTechnologies/WebObjects/WebObjects_3.5/Reference/Frameworks/ObjC/Foundation/Classes/NSBundle/Description.html#//apple_ref/occ/cl/NSBundle)에 주요 클래스를 요청하고 반환된 `Class` 객체를 사용하여 클래스의 인스턴스를 생성해야 한다.

### Prerequisite Articles

[Message](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/Message.html#//apple_ref/doc/uid/TP40008195-CH59-SW1)

#### Related Articles

[Property list](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/PropertyList.html#//apple_ref/doc/uid/TP40008195-CH44-SW1)  
[Nib file](https://developer.apple.com/library/archive/documentation/General/Conceptual/DevPedia-CocoaCore/NibFile.html#//apple_ref/doc/uid/TP40008195-CH34-SW1)

#### Definitive Discussion

[About Bundles](https://developer.apple.com/library/archive/documentation/CoreFoundation/Conceptual/CFBundles/AboutBundles/AboutBundles.html#//apple_ref/doc/uid/10000123i-CH100)

