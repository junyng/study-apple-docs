# Time and Media Representations

동영상 파일이나 비디오 스트림과 같은 시간 기반 시청각 데이터는 `AVAsset`에 의해 AV Foundation 프레임워크에서 표현된다. 그 구조체는 많은 프레임워크가 작용하는 것을 지시한다. AVFoundation이 샘플 버퍼와 같은 시간과 미디어를 나타내기 위해 사용하는 몇 가지 저수준의 데이터 구조는 Core Media 프레임워크에서 나온다.

### 에셋 표현

[`AVAsset`](https://developer.apple.com/documentation/avfoundation/avasset)은 AVFoundation 프레임워크의 핵심 클래스다. 그것은 동영상 파일이나 비디오 스트림과 같은 시간 기반 시청각 데이터의 형식 독립적인 추상화를 제공한다. 주요 관계는 그림 6-1에 나와 있다. 대부분의 경우, 다음과 같은 하위 클래스 중 하나를 사용하여 작업하라: 새로운 에셋을 생성할 때 컴포지션 서브클래스를 사용하고\([Editing](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/00_Introduction.html#//apple_ref/doc/uid/TP40010188-CH1-SW1) 참조\), [`AVURLAsset`](https://developer.apple.com/documentation/avfoundation/avurlasset)을 사용하여 주어진 URL의 미디어에서 새로운 인스턴스를 생성한다.\(MPMedia 프레임워크 또는 에셋 라이브러리 프레임워크 포함 - [Using Assets](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/01_UsingAssets.html#//apple_ref/doc/uid/TP40010188-CH7-SW1) 참조\).

**그림 6-1** AVAsset은 시각 기반 추상화된 시청각 데이터를 제공한다.

![](../../.gitbook/assets/avassethierarchy_2x.png)

에셋에는 오디오, 비디오, 텍스트, 닫힌 캡션 및 자막을 포함한 \(이에 제한되지는 않음\) 각각의 통일된 미디어 유형이 함께 표시하거나 처리하기 위한 트랙 컬렉션이 포함되어 있다. 에셋 객체는 지속 시간 또는 제목과 같은 전체 리소스에 대한 정보 뿐만아니라 자연적인 크기와 같은 표시에 대한 힌트를 제공한다. 에셋은 또한 [`AVMetadataItem`](https://developer.apple.com/documentation/avfoundation/avmetadataitem)의 인스턴스로 대표되는 메타데이터를 가질 수 있다.

트랙은 그림 6-2와 같이 [`AVAssetTrack`](https://developer.apple.com/documentation/avfoundation/avassettrack)의 인스턴스로 표현된다. 일반적인 간단한 경우, 한 트랙은 오디오 구성 요소를 나타내고 다른 트랙은 비디오 구성 요소를 나타낸다. 복잡한 구성에서는 오디오와 비디오의 겹치는 트랙이 여러 개 있을 수 있다.

**그림 6-2**  AVAssetTrack

![](../../.gitbook/assets/avassetandtracks_2x.png)

트랙은 유형\(비디오 또는 오디오\), 시각적 및/또는 청각적 특성\(적절한 경우\), 메타데이터 및 타임라인\(상위 에셋 관점에서 표현됨\)와 같은 많은 속성을 가지고 있다. 트랙에는 포멧 설명 배열도 가지고 있다. 배열에는 `CMFormatDescription` 객체\([`CMFormatDescriptionRef`](https://developer.apple.com/documentation/coremedia/cmformatdescriptionref) 참조\)가 있으며, 각 객체는 트랙에서 참조하는 미디어 샘플의 포멧을 설명한다. 균일한 미디어\(예: 모두 동일한 설정으로 인코딩된 모든 미디어\)를 포함하는 트랙은 1개의 개수를 가진 배열을 제공한다.

트랙 자체는 [`AVAssetTrackSegment`](https://developer.apple.com/documentation/avfoundation/avassettracksegment)의 인스턴스로 대표되는 세그먼트로 나눌 수 있다. 세그먼트는 원본에서 에셋 트랙 타임라인으로의 시간 매핑이다.

### 시간의 표현

`AVFoundation`의 `Time`은 `Core Media` 프레임워크의 원시 구조로 표현된다.

#### CMTime은 시간의 길이를 나타낸다

[`CMTime`](https://developer.apple.com/documentation/coremedia/cmtime)은 시간을 합리적인 숫자로 나타내는 C 구조체로 분자\(`int64_t` 값\)와 분모\(`int64_t` 시간 척도\)가 있다. 개념적으로, 시간 척도는 분자의 각 단위가 차지하는 두 번째 단위의 비율을 명시한다. 따라서 시간 척도가 4이면 각 단위는 1/4초, 시간 척도가 10이면 각 단위는 1/10초 등을 나타낸다. 당신은 종종 600의 시간 스케일을 사용하는데, 이것은 필름의 경우 24 fps, NTSC의 경우 30 fps\(미국 및 일본\), 그리고 PAL의 경우 25 fps\(유럽의 경우 TV에 사용됨\)의 몇 가지 일반적인 프레임률의 배합이기 때문이다. 600의 시간 척도를 사용하여 이러한 시스템의 프레임 수를 정확하게 나타낼 수 있다.

`CMTime` 구조체는 단순한 시간 값 외에도 +infinity, -infinity 및 무한의 숫자 값을 나타낼 수 있다. 또한 어느 시점에서 시간이 반올림되었는지 여부를 나타낼 수 있으며, 시대적 숫자를 유지하고 있다.

**CMTime 사용**

[`CMTimeMake`](https://developer.apple.com/documentation/coremedia/1400785-cmtimemake) 또는 [`CMTimeMakeWithSeconds`](https://developer.apple.com/documentation/coremedia/1400797-cmtimemakewithseconds)와 같은 관련 기능 중 하나를 사용하여 시간을 생성할 수 있다. \(float 값을 사용하여 시간을 생성하고 기본 시간 스케일을 지정할 수 있다.\) 시간 기반 산술과 시간 비교에는 다음 예에서 설명한 바와 같이 몇 가지 함수가 있다:

```objectivec
CMTime time1 = CMTimeMake(200, 2); // 200 half-seconds
CMTime time2 = CMTimeMake(400, 4); // 400 quarter-seconds
 
// time1 and time2 both represent 100 seconds, but using different timescales.
if (CMTimeCompare(time1, time2) == 0) {
    NSLog(@"time1 and time2 are the same");
}
 
Float64 float64Seconds = 200.0 / 3;
CMTime time3 = CMTimeMakeWithSeconds(float64Seconds , 3); // 66.66... third-seconds
time3 = CMTimeMultiply(time3, 3);
// time3 now represents 200 seconds; next subtract time1 (100 seconds).
time3 = CMTimeSubtract(time3, time1);
CMTimeShow(time3);
 
if (CMTIME_COMPARE_INLINE(time2, ==, time3)) {
    NSLog(@"time2 and time3 are the same");
}
```

사용 가능한 모든 함수의 목록은 [_CMTime Reference_](https://developer.apple.com/documentation/coremedia/cmtime-u58)를 참조하라.

**CMTime의 특수 값**

Core Media는 `kCMTimeZero`, `kCMTimeInvalid`, `kCMTimePositiveInfinity` 및 `kCMTimeNegativeInfinity`의 특수 값에 대한 상수를 제공한다. 예를 들어, `CMTime` 구조체가 유효하지 않은 시간을 나타내는 많은 방법이 있다. `CMTime`이 유효한지 또는 숫자가 아닌 값을 테스트하려면 [`CMTIME_IS_INVALID`](https://developer.apple.com/documentation/coremedia/cmtime_is_invalid), [`CMTIME_IS_POSITIVE_INFINITY`](https://developer.apple.com/documentation/coremedia/cmtime_is_positive_infinity), 또는 [`CMTIME_IS_INDEFINITE`](https://developer.apple.com/documentation/coremedia/cmtime_is_indefinite) 과 같은 매크로를 사용하라.

```objectivec
CMTime myTime = <#Get a CMTime#>;
if (CMTIME_IS_INVALID(myTime)) {
    // Perhaps treat this as an error; display a suitable alert to the user.
}
```

임의 `CMTime` 구조체의 값을 `kCMTimeInvalid` 와 비교해서는 안된다.

**CMTime을 객체로 나타내기** 

주석 또는 코어 파운데이션 컨테이너에서 CMTime 구조체를 사용해야 하는 경우 [`CFDictionaryRef`](https://developer.apple.com/documentation/corefoundation/cfdictionaryref) 및 [`CMTimeCopyAsDictionary`](https://developer.apple.com/documentation/coremedia/1400845-cmtimecopyasdictionary) 함수을 사용하여 `CFDictionary` 불투명 타입\([`CFDictionaryRef`](https://developer.apple.com/documentation/corefoundation/cfdictionaryref) 참조\)으로 `CMTime` 구조체를 변환할 수 있다. [`CMTimeCopyDescription`](https://developer.apple.com/documentation/coremedia/1400791-cmtimecopydescription) 함수를 사용하여 CMTime 구조체의 문자열 표현도 얻을 수 있다. 

**Epochs**

`CMTime` 구조체의 Epoch 숫자는 일반적으로 0으로 설정되지만 관련없는 타임 라인을 구별하는 데 사용할 수 있다. 예를 들어, 0의 시간 `N`과 루프 1의 시간 `N`을 구별하기 위해 프리젠테이션 루프를 사용하여 각 사이클을 통해 Epoch를 증가시킬 수 있다.

#### CMTimeRange는 시간 범위를 나타낸다

[`CMTimeRange`](https://developer.apple.com/documentation/coremedia/cmtimerange)는 C 구조체로 시작 시간과 지속 시간이 있으며 `CMTime` 구조체로 표현된다. 시간 범위에는 시작 시간과 지속 시간이 포함되지 않는다.

[`CMTimeRangeMake`](https://developer.apple.com/documentation/coremedia/1462785-cmtimerangemake) 또는 [`CMTimeRangeFromTimeToTime`](https://developer.apple.com/documentation/coremedia/1462817-cmtimerangefromtimetotime)를 사용하여 시간 범위를 생성하라. `CMTime`값의 epochs에 제약이 있다.

* `CMTimeRange` 구조체는 서로 다른 epochs를 뛰어넘을 수 없다.
* 타임스탬프를 나타내는 CMTime 구조체의 epoch는 0이 아닐 수 있지만, 시작 필드가 동일한 범위에서만 범위 작업\(예: [`CMTimeRangeGetUnion`](https://developer.apple.com/documentation/coremedia/1462837-cmtimerangegetunion)\)을 수행할 수 있다.
* 지속시간을 나타내는 `CMTime` 구조체의 시점은 항상 0이어야하며 값은 음수가 아니어야 한다.

**시간 범위 작업**

Core Media는 시간 범위가 주어진 시간 범위 또는 다른 시간 범위가 포함되어 있는지 여부, 두 시간 범위가 동일한지 여부, [`CMTimeRangeContainsTime`](https://developer.apple.com/documentation/coremedia/1462775-cmtimerangecontainstime), [`CMTimeRangeEqual`](https://developer.apple.com/documentation/coremedia/1462841-cmtimerangeequal), [`CMTimeRangeContainsTimeRange`](https://developer.apple.com/documentation/coremedia/1462830-cmtimerangecontainstimerange), [`CMTimeRangeGetUnion`](https://developer.apple.com/documentation/coremedia/1462837-cmtimerangegetunion)와 같은 시간 범위의 조합과 교차점을 계산하는 데 사용할 수 있는 기능을 제공한다.

시간 범위에 시작 시간과 지속 시간이 포함되지 않는 경우, 다음식은 항상 거짓으로 평가된다:

```objectivec
CMTimeRangeContainsTime(range, CMTimeRangeGetEnd(range))
```

사용 가능한 모든 함수 목록은 [_CMTimeRange Reference_](https://developer.apple.com/documentation/coremedia/cmtimerange-qts)를 참조하라.

**CMTimeRange의 특수한 값**

코어 미디어는 각 0의 길이 범위와 유효하지 않은 범위인 [`kCMTimeRangeZero`](https://developer.apple.com/documentation/coremedia/kcmtimerangezero)와 [`kCMTimeRangeInvalid`](https://developer.apple.com/documentation/coremedia/kcmtimerangeinvalid)에 대한 상수를 제공한다. `CMTimeRange` 구조체가 유효하지 않거나 0 또는 무한\(`CMTime` 구조체 중 하나가 비한정인 경우\)일 수 있는 방법은 여러 가지 방법이 있다.

```objectivec
CMTimeRange myTimeRange = <#Get a CMTimeRange#>;
if (CMTIMERANGE_IS_EMPTY(myTimeRange)) {
    // The time range is zero.
}
```

임의 `CMTimeRange` 구조체의 값을 `kCMTimeRangeInvalid`와 비교해서는 안된다.

**CMTimeRange를 객체로 표현**

주석이나 Core Foundation 컨테이너에서 `CMTimeRange` 구조체를 사용해야 하는 경우, `CMTimeRange` 구조체를 각각 [`CMTimeRangeCopyAsDictionary`](https://developer.apple.com/documentation/coremedia/1462781-cmtimerangecopyasdictionary)와 [`CMTimeRangeMakeFromDictionary`](https://developer.apple.com/documentation/coremedia/1462777-cmtimerangemakefromdictionary)를 사용하여 `CFDictionary` 불투명 타입\([`CFDictionaryRef`](https://developer.apple.com/documentation/corefoundation/cfdictionaryref) 참조\)로 변환할 수 있다. 또한 [`CMTimeRangeCopyDescription`](https://developer.apple.com/documentation/coremedia/1462823-cmtimerangecopydescription)함수를 사용하여 CMTime 구조체의 문자열 표현을 얻을 수도 있다.

### 미디어의 표현

비디오 데이터와 관련 메타데이터는 AVFoundation에서 코어 미디어 프레임워크의 불투명 객체에 의해 표현된다. Core Media는 `CMSampleBuffer`를 사용한 비디오 데이터를 나타낸다\([`CMSampleBufferRef`](https://developer.apple.com/documentation/coremedia/cmsamplebuffer) 참조\). `CMSampleBuffer`는 Core Foundation 스타일의 불투명 타입으로, 인스턴스는 Core Video 픽셀 버퍼로서 비디오 데이터의 프레임에 대한 샘플 버퍼를 포함한다.\([`CVPixelBufferRef`](https://developer.apple.com/documentation/corevideo/cvpixelbuffer) 참조\). [`CMSampleBufferGetImageBuffer`](https://developer.apple.com/documentation/coremedia/1489236-cmsamplebuffergetimagebuffer)를 사용하여 샘플버퍼에 접근하는 경우:

```objectivec
CVPixelBufferRef pixelBuffer = CMSampleBufferGetImageBuffer(<#A CMSampleBuffer#>);
```

픽셀 버퍼에서 실제 비디오 데이터에 접근할 수 있다. 예를 들어, [Converting CMSampleBuffer to a UIImage Object](https://developer.apple.com/library/archive/documentation/AudioVideo/Conceptual/AVFoundationPG/Articles/06_MediaRepresentations.html#//apple_ref/doc/uid/TP40010188-CH2-SW4)를 참조하라.

비디오 데이터 외에도 비디오 프레임의 여러 가지 다른 측면을 검색할 수 있다:

* **타이밍 정보.** [`CMSampleBufferGetPresentationTimeStamp`](https://developer.apple.com/documentation/coremedia/1489252-cmsamplebuffergetpresentationtim) 및 [`CMSampleBufferGetDecodeTimeStamp`](https://developer.apple.com/documentation/coremedia/1489404-cmsamplebuffergetdecodetimestamp)를 각각 사용하여 원래 프레젠테이션 시간과 디코드 시간에 대한 정확한 타임스탬프를 얻어라.
* **포멧 정보.** 포멧 정보는 CMFormatDescription 객체에 캡슐화 된다.\([`CMFormatDescriptionRef`](https://developer.apple.com/documentation/coremedia/cmformatdescriptionref) 참조\). 포멧 설명에서 예를 들어 `CMVideoFormatDescriptionGetCodecType` 및 [`CMVideoFormatDescriptionGetDimensions`](https://developer.apple.com/documentation/coremedia/1489287-cmvideoformatdescriptiongetdimen)를 사용하여 픽셀 유형과 비디오 치수를 얻을 수 있다.
* **메타데이터.** 메타데이터는 _attachment_로 딕셔너리에 저장된다. [`CMGetAttachment`](https://developer.apple.com/documentation/coremedia/1470707-cmgetattachment)를 사용하여 딕셔너리를 검색하라:

```objectivec
CMSampleBufferRef sampleBuffer = <#Get a sample buffer#>;
CFDictionaryRef metadataDictionary =
    CMGetAttachment(sampleBuffer, CFSTR("MetadataDictionary", NULL);
if (metadataDictionary) {
    // Do something with the metadata.
}
```

### CMSampleBuffer를 UIImage 객체로 변환

다음 코드는 `CMSampleBuffer`를 `UIImage` 객체로 변환하는 방법을 보여준다. 사용 전에 당신의 요구 사항을 신중히 고려해야 한다. 변환을 수행하는 것은 비교적 비용이 많이 드는 작업이다. 예를 들어, 매초마다 찍은 비디오 데이터의 프레임에서 정지된 이미지를 생성하는 것이 적절하다. 당신은 이것을 캡처 장치에서 나오는 모든 비디오 프레임을 실시간으로 조작하는 수단으로 사용해서는 안된다.

```objectivec
// Create a UIImage from sample buffer data
- (UIImage *) imageFromSampleBuffer:(CMSampleBufferRef) sampleBuffer
{
    // Get a CMSampleBuffer's Core Video image buffer for the media data
    CVImageBufferRef imageBuffer = CMSampleBufferGetImageBuffer(sampleBuffer);
    // Lock the base address of the pixel buffer
    CVPixelBufferLockBaseAddress(imageBuffer, 0);
 
    // Get the number of bytes per row for the pixel buffer
    void *baseAddress = CVPixelBufferGetBaseAddress(imageBuffer);
 
    // Get the number of bytes per row for the pixel buffer
    size_t bytesPerRow = CVPixelBufferGetBytesPerRow(imageBuffer);
    // Get the pixel buffer width and height
    size_t width = CVPixelBufferGetWidth(imageBuffer);
    size_t height = CVPixelBufferGetHeight(imageBuffer);
 
    // Create a device-dependent RGB color space
    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
 
    // Create a bitmap graphics context with the sample buffer data
    CGContextRef context = CGBitmapContextCreate(baseAddress, width, height, 8,
      bytesPerRow, colorSpace, kCGBitmapByteOrder32Little | kCGImageAlphaPremultipliedFirst);
    // Create a Quartz image from the pixel data in the bitmap graphics context
    CGImageRef quartzImage = CGBitmapContextCreateImage(context);
    // Unlock the pixel buffer
    CVPixelBufferUnlockBaseAddress(imageBuffer,0);
 
    // Free up the context and color space
    CGContextRelease(context);
    CGColorSpaceRelease(colorSpace);
 
    // Create an image object from the Quartz image
    UIImage *image = [UIImage imageWithCGImage:quartzImage];
 
    // Release the Quartz image
    CGImageRelease(quartzImage);
 
    return (image);
}
```



