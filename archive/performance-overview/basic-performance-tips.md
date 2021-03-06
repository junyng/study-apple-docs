# Basic Performance Tips

이 장은 프로그램을 어떻게 조정할 것인지에 대한 실질적인 조언을 제공한다. 또한 성능 도구로 모니터링해야 할 영역에 대한 제안을 제공하고 성능 향상을 위한 실질적인 팁 목록을 제공한다.

### Common Areas to Monitor

많은 성능 문제를 프로그램의 특정 부분으로 추적할 수 있다. 코드를 설계하고 구현할 때 해당 영역을 모니터링하여 설정한 성능 목표를 충족해야 한다.

#### Code for Your Program’s Key Tasks

프로그램을 설계할 때, 사용자가 가장 많이 접할 작업이나 워크플로우를 고려하라. 구현 단계에서 해당 작업의 코드를 모니터링하고 성능이 허용 수준 이하로 떨어지지 않도록 하라. 그렇다면 즉시 조치를 취하여 문제를 해결해야 한다.

프로그램에 의해 수행되는 주요 업무는 프로그램마다 다르다. 예를 들어, 워드프로세서는 텍스트 입력과 표시 중에 속도가 빨라야 하는 반면 파일 유틸리티 프로그램은 하드 디스크의 파일 및 디렉터리를 빠르게 검색해야 할 수 있다. 사용자가 어떤 작업을 가장 많이 수행할지 결정하는 것은 사용자의 몫이다.

프로그램에서 느린 작업을 식별하고 수정하는 방법에 대한 정보는 [_Code Speed Performance Guidelines_](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/CodeSpeed/CodeSpeed.html#//apple_ref/doc/uid/10000150i)를 참조하라.

#### Drawing Code

대부분의 프로그램은 어느 정도 그림을 그린다. 프로그램이 표준 윈도우와 제어 장치만 사용한다면 그리기 성능에 대해 크게 걱정할 필요가 없을 것이다. 그러나, 사용자 정의 그리기를 수행하는 경우 그리기 코드를 모니터링하여 허용 가능한 수준에서 수행되는지 확인하라. 특히, 다음 중 하나를 지원할 경우 그리기 코드를 최적화하는 방법을 조사해야 한다.

* 실시간 리사이징
* 사용자 정의 뷰 그리기 코드, 특히 전체 뷰를 업데이트하지 않고 뷰의 일부를 업데이트할 수 있는 경우
* 텍스처 그래픽
* 완전히 불투명한 뷰

그리기 성능을 최적화하는 방법에 대한 정보는 [_Drawing Performance Guidelines_](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/Drawing/Articles/DrawingPerformance.html#//apple_ref/doc/uid/10000151i)를 참조하라.

#### Launch Time Initialization Code

실행 시간은 프로그램의 데이터 구조를 초기화하고 애플리케이션이 사용자 입력을 받을 수 있도록 준비하는 시간이다. 그러나, 대부분의 프로그램들은 필요한 것보다 시작 시간에 훨씬 더 많은 작업을 한다. 대부분의 경우, 실행 시 수행되는 작업은 애플리케이션이 사용자 인터페이스를 게시하고 이벤트 처리를 시작한 후까지 연기할 수 있다. 이 지연은 사용자에게 당신의 애플리케이션이 빠르다는 인식을 심어주는데, 이것은 좋은 첫 인상이다.

OS X 버전 10.3.3 이하에서 실행해야 하는 애플리케이션의 경우, 시작 시간을 단축하는 또 다른 방법은 애플리케이션을 미리 바인딩하는 것이다. 사전 바인딩에는 라이브러리 주소 범위를 미리 계산하고 해당 값을 애플리케이션 바이너리에 저장하는 것이 포함된다. 이 단계에서는 동적 로더 \(`dyld`\)가 시작 시점에 해당 주소 범위를 계산할 필요가 없다. OS X 버전 10.3.4에 대한 `dyld`의 개선은 그 버전과 이후 버전에서 사전 바인딩을 불필요하게 만들었다. 마찬가지로 iOS에서는 사전 바인딩이 불필요하다.

실행 시간 성능을 향상시키는 방법에 대한 자세한 내용은 [_Launch Time Performance Guidelines_](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/LaunchTime/LaunchTime.html#//apple_ref/doc/uid/10000148i)를 참조하라.

#### File Access Code

파일 시스템은 정보를 메모리와 CPU에 정보를 입력하기 위한 병목 현상이다. 파일에 접근하는 데 걸리는 시간에는 수천만 개의 명령이 실행될 수 있다. 따라서 프로그램이 파일을 사용하는 방식을 조사하고 해당 파일이 실제로 필요하고 적절하게 사용되는지 확인하는 것이 필수적이다.

파일 관련 성능을 향상시키는 한 가지 방법은 사용하는 파일 수를 최소화하는 것이다. 파일에 접근할 때 다음을 명심하라:

* 시스템이 파일 데이터를 캐싱하는 방법과 캐시의 사용을 최적화하는 방법을 이해하라. 데이터를 두 번 이상 참조할 계획이 없는 한 직접 캐싱하지 마라.
* 가능하면 데이터를 순차적으로 읽고 써라. 파일을 이리저리 점프하면 새로운 위치를 찾는 데 시간이 더 걸린다.
* 가능한 한 파일에서 더 큰 데이터 블록을 읽으며, 한 번에 너무 많은 데이터를 읽는 것은 다른 문제를 일으킬 수 있다는 것을 명심하라. 예를 들어, 32MB 파일의 전체 내용을 읽으면 작업이 완료되기 전에 해당 내용의 페이징이 트리거될 수 있다.
* 불필요하게 파일을 닫고 다시 열지 마라. 캐싱이 활성화된 경우 데이터가 변경되지 않았더라도 캐시가 새로 고침될 수 있다.

파일 관련 성능 문제를 식별하고 수정하는 방법에 대한 내용은 [_File-System Performance Guidelines_](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/FileSystem/FileSystem.html#//apple_ref/doc/uid/10000161i)를 참조하라.

#### Application Footprint

당신의 코드의 크기는 시스템 성능에 엄청난 영향을 미칠 수 있다. 프로그램에서 더 많은 메모리 페이지를 사용할수록 시스템 및 다른 프로그램에서 사용할 수 있는 메모리 페이지가 더 적어지고, 이 메모리 압박은 결국 페이징과 전반적인 시스템 저하를 초래할 수 있다.

코드 설치 공간을 관리하는 것은 코드와 데이터 구조를 정리하는 일이다. 메모리에 맞는 부분을 가지고 있는지, 그리고 불필요하게 어떤 메모리 페이지도 읽거나 쓰이게 하지 않도록 해야 한다. 큰 메모리 공간을 야기하는 문제 중 일부는 다음과 같다:

* 코드 페이지에는 사용되지 않는 코드가 있다. 컴파일러는 일반적으로 컴파일 모듈에 의해 코드를 구성하는데, 항상 최고의 기술은 아니다. 어떤 코드가 함께 실행되는지 기반으로 모듈을 구성하는 것이 더 좋은 선택일 수 있다.
* 정적 또는 상수 데이터는 쓰기 가능한 페이지에 저장된다. 페이징 중에 이 데이터는 불필요하게 디스크에 기록된다. 이 데이터를 빨리 삭제할 수 있는 쓰기불가능한 페이지로 이동하려면 할 수 있는 작업을 수행하라.
* 프로그램은 실제로 필요한 것보다 더 많은 기호를 내보낸다. 기호는 공간을 차지하며 프로그램에 호출해야 하는 외부 코드 모듈에 의해서만 필요하다. 외부에서 사용해서는 안 되는 기호를 모두 제거하라.
* 코드가 컴파일러 및 링커에 의해 제대로 최적화 되지 않았다. 컴파일러의 최적화 설정을 시도하고 프로그램에 가장 잘 맞는 설정을 확인하라.
* 프로그램에 너무 많은 프레임워크가 포함되어 있다. 프로그램이 실제로 사용하는 프레임워크만 로드하라.

코드 설치 공간 문제를 찾아 해결하는 방법에 대한 자세한 내용은 [_Code Size Performance Guidelines_](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/CodeFootprint/CodeFootprint.html#//apple_ref/doc/uid/10000149i)를 참조하라.

#### Memory Allocation Code

프로그램은 영구 데이터 구조와 임시 데이터 구조를 모두 저장하기 위해 메모리를 할당한다. 각 메모리 할당은 CPU 시간과 메모리 소비 모두에서 그것과 관련된 비용을 갖는다. 프로그램이 메모리를 할당할 때와 메모리를 사용하는 방법을 이해하는 것은 두 가지 비용을 모두 줄이는 데 도움이 될 수 있다.

프로그램의 메모리 사용량을 이해하는 것은 그 사용량을 줄이는 방법을 결정하는 데 도움이 될 수 있다. 사용 가능한 성능 도구를 사용하여 자동 정렬된 Objective-C 객체가 너무 많은 페이징을 발생시키기 전에 할당 해제되는지 여부를 확인할 수 있다. 또한 이러한 도구를 사용하여 코드의 버그로 인한 메모리 누수를 찾을 수 있다. 또한, `malloc`에 호출하는 횟수를 보면 새로운 메모리 블록을 만들기보다는 기존 메모리 블록을 재사용할 수 있는 장소를 지적할 수 있다.

메모리를 할당할 때 따라야 할 중요한 규칙 중 하나는 lazy가 되도록 하는 것이다. 실제로 사용할 메모리가 필요할 때까지 메모리 할당을 연기한다. 메모리 할당으로 lazy되는 몇 가지 추가적인 방법은 [Be Lazy](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/PerformanceOverview/BasicTips/BasicTips.html#//apple_ref/doc/uid/TP40001410-CH204-BCIHHDAA)를 참조하라.

메모리 할당 패턴 최적화에 대한 내용은 [_Memory Usage Performance Guidelines_](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/ManagingMemory/ManagingMemory.html#//apple_ref/doc/uid/10000160i)를 참조하라.

### Fundamental Optimization Tips

새로운 프로그램을 실행하기 전에 추가해야 할 성능 향상 사항이 몇가지 있다. 모든 경우에 이러한 향상된 기능을 모두 활용할 수는 없지만 설계 단계에서는 최소한 이러한 기능을 고려해야 한다.

#### Be Lazy

성능을 향상시키는 매우 간단한 방법은 애플리케이션이 불필요한 작업을 수행하지 않도록 하는 것이다. 애플리케이션 시간의 각 순간은 미래의 요청을 예측하는 것이 아니라 사용자의 현재 요청에 응답하는 데 사용되어야 한다. 기본 설정 창이 포함된 닙 파일과 같은 자원이 바로 필요하지 않으면 로드하지 마라. 이러한 작업은 파일 시스템에 접근하기 때문에 실행하는데 시간이 걸리고, 사용자가 그 기본 설정 창을 열지 않으면 닙 파일을 로드하는 과정은 시간 낭비이다.

기본 규칙은 사용자가 애플리케이션에서 무언가를 요청할 때까지 기다린 후 필요한 자원을 사용하여 요청을 이행하는 것이다. 측정 가능한 성능 이점이 있는 경우에만 데이터를 캐시하라. 나머지 애플리케이션이 더 빨리 실행될 것이라는 가정하에 캐시를 미리 로드하면 메모리가 낮은 상황에서 실제로 성능을 저하시킬 수 있다. 이러한 상황에서는 캐시된 데이터를 사용하기 전에 디스크에 페이징될 수 있다. 따라서 데이터를 캐싱하여 절감한 모든 비용은 이제 데이터를 사용하기 전에 디스크에서 두 번 읽어야 하기 때문에 손실로 전환된다. 데이터를 캐시하려면 작업을 한 번 수행한 후 수행하라.

그 밖의 lazy 되어야 할 몇 가지 사항은 다음과 같다:

* 실제로 메모리가 필요할 때까지 메모리 할당을 연기하라.
* 메모리 블록을 0으로 초기화하지 마라. `calloc` 함수를 호출하여 lazily하게 하라.
* 시스템에 코드를 느리게 로드할 수 있는 기회를 제공하라. 시스템이 현재 작업에 필요한 코드만 로드하도록 코드를 프로파일링하고 구성하라.
* 정보가 실제로 필요할 때까지 파일 내용 읽기를 연기하라.

#### Take Advantage of Perceived Performance

성능에 대한 인식은 실제 성능 못지않게 많은 경우에 효과적이다. 많은 프로그램 작업은 백그라운드, 디스패치 큐 또는 유휴 시간에 수행될 수 있다. 이렇게 하면 애플리케이션의 메인 쓰레드가 사용자 상호작용을 처리할 수 있고 프로그램 인터페이스가 사용자에게 더 잘 반응하는 것처럼 느끼게 된다. 프로그램을 설계할 때 백그라운드로 효과적으로 이동할 수 있는 작업을 생각하라. 예를 들어, 프로그램이 많은 파일을 검색하거나 긴 계산을 수행해야 하는 경우 디스패치 큐를 사용하여 검색하라.

인식된 성능을 향상시키는 또 다른 방법은 애플리케이션이 빨리 실행되도록 하는 것이다. 실행 시, 애플리케이션 인터페이스의 즉각적인 표시에 기여하지 않는 작업을 연기하라. 예를 들어, 애플리케이션이 시작을 완료하고 메인 윈도우를 표시한 후까지 대용량 데이터 구조 작성을 연기하라. 메인 윈도우를 사용하여 실행 시 계산하거나 검색해야 하는 일부 데이터를 표시하는 경우 먼저 프로그레스 인디케이터 또는 데이터가 로드되고 있음을 나타내는 기타 상태 메시지와 함께 윈도우를 표시하라. 플러그인을 사용하는 애플리케이션의 경우 코드가 실제로 필요할 때까지 플러그인이 로드되지 않도록 하라.

#### Use Event-Based Handlers

모든 현대의 맥 앱은 Cocoa 이벤트 시스템 또는 Carbon Event Manager를 사용해야 한다. \(마찬가지로, 아이폰 애플리케이션은 UIKit 프레임워크에서 제공하는 터치 기반 이벤트 시스템을 사용해야 한다.\) 애플리케이션에서 시스템을 폴링하여 이벤트를 검색하지 마라. 그렇게 하는 것은 매우 비효율적이다. 실제로 처리할 이벤트가 없을 때 폴링 코드는 CPU 시간의 100% 낭비이다. 현대의 이벤트 기반 API는 다음과 같은 이점을 제공하도록 설계되었다:

* 당신의 프로그램이 사용자들에게 더 잘 반응하도록 만든다.
* 애플리케이션의 CPU 사용량을 줄인다.
* 특정 시간에 메모리에 로드되는 코드 페이지 수, 즉 애플리케이션의 워킹 셋을 최소화한다.
* 그 시스템이 적극적으로 파워를 관리하도록 허용한다.

사용자 이벤트 외에 다른 상황에서도 폴링을 피해야 한다. OS X 및 iOS의 쓰레드는 런 루프를 사용하며 타이머, 네트워크 이벤트 및 기타 수신 데이터에 온디맨드 응답을 제공한다. 많은 프레임워크 특정 작업에 대해 비동기 프로그래밍 모델을 사용하여 작업이 완료되면 지정된 핸들러 함수 또는 메서드를 알린다. OS X v10.6 이상에서는 디스패치 소스가 중요한 이벤트를 비동기적으로 수신하여 디스패치 큐에서 실행할 수 있는 방법도 제공한다.

#### Improve the Concurrency of Your Program’s Tasks

멀티 코어를 가진 컴퓨터에서 동시성은 프로그램의 인식된 성능과 실제 성능을 모두 향상시키는 또 다른 방법이다. 여러 작업을 동시에 수행할 수 있는 프로그램은 멀티 코어 컴퓨터에서 이러한 작업을 병렬로 실행할 수 있다. 컴퓨터에 코어가 하나만 있더라도 코드를 여러 개의 비동기 작업에 포함시키면 올바르게 수행되었을 때 인식된 속도 향상을 제공할 수 있다. 특히, 디스패치 큐를 사용하여 사용자 정의 작업을 수행하고 메인 쓰레드를 자유롭게 사용하여 사용자 이벤트를 처리하고 사용자 인터페이스를 주로 업데이트하라.

그러나 동시성 지원을 추가하기 전에 프로그램이 해당 작업을 효과적으로 구현할 수 있는 방법에 대해 몇 가지 생각을 해야 한다. 코드를 다른 작업에 포함시키려면 프로그램 데이터 구조와 코드 경로를 고려해야 한다. 데이터 구조를 공유하는 작업은 이러한 구조에 대한 접근을 동기화하기 위해 시리얼 디스패치 큐를 사용해야 할 수 있다.

프로그램에서 동시성 구현 방법에 대한 내용은 [_Concurrency Programming Guide_](https://developer.apple.com/library/archive/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008091)를 참조하라.

#### Use the Accelerate Framework

애플리케이션이 스칼라 데이터에 대해 많은 수학적 계산을 수행하는 경우 Accelerate 프레임워크\(`Accelerate.framework`\)를 사용하여 이러한 계산을 가속화하는 것을 고려해야 한다. Accelerate 프레임워크는 사용 가능한 벡터 처리 장치\(Intel x86 SSE 확장자 같은\)를 활용하여 병렬로 여러 가지 계산을 수행한다. 벡터 유닛 대신 프레임워크로 코딩함으로써 각 플랫폼 아키텍처에 대해 별도의 코드 경로를 생성하지 않아도 된다. Accelerate 프레임워크는 OS X가 지원하는 모든 아키텍처에 대해 고도로 조정된다.

Instruments와 같은 도구는 Accelerate 프레임워크를 사용하는 데 도움이 될 수 있는 프로그램 부분을 지적하는 데 도움이 될 수 있다. 이러한 도구 및 기타 도구에 대한 자세한 내용은 [Performance Tools](https://developer.apple.com/library/archive/documentation/Performance/Conceptual/PerformanceOverview/PerformanceTools/PerformanceTools.html#//apple_ref/doc/uid/TP40001410-CH205-BCIIHAAJ)를 참조하라.

#### Modernize Your Application

프로그램이 이전 버전의 Mac OS에서 실행되도록 설계된 경우 OS X 컨벤션을 지원하도록 코드를 업데이트하라. 특히, Carbon과 같은 구식 기술이나 QuickDraw와 같은 레거시 기술 사용을 피해야 한다. 대신, Cocoa나 Cocoa Touch를 사용하여 애플리케이션을 만들어야 한다. 바이너리 포멧도 Mach-O로 업데이트해야 한다. Mach-O 포멧은 Intel 기반 매킨토시 컴퓨터와 iOS 기반 장치에서 지원되는 유일한 형식이다.

Mac 앱에서 사용할 수 있는 기술 목록은 [_Mac Technology Overview_](https://developer.apple.com/library/archive/documentation/MacOSX/Conceptual/OSX_Technology_Overview/About/About.html#//apple_ref/doc/uid/TP40001067)를 참조하라. iOS에서 사용할 수 있는 기술 목록은 _iOS Technology Overview_를 참조하라.



