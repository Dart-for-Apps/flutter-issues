# 각종 이슈 정리

- [설치 이슈](#설치-이슈)
- [빌드 이슈](#빌드-이슈)
- [안드로이드 이슈](#안드로이드-이슈)
- [iOS 이슈](#iOS-이슈)
- [핫 리로드 이슈](#핫-리로드-이슈)
- [Async 이슈](#Async-이슈)
- [Life Cycle](#Life-Cycle)

## 설치 이슈

### `flutter doctor` 실행 시 **android licenses unknown status** 관련 에러

1. 자바 JDK 버전을 1.8 버전으로 재설치
2. 시스템 환경 설정의 JAVA_HOME을 설치한 JDK 1.8 버전 폴더로 설정
3. 시스템 환경 설정의 PATH에 JDK 1.8 버전 폴더의 하위 폴더인 bin 폴더 추가 
4. 관리자 권한으로 CMD 실행
5. `flutter doctor --android-licenses` 실행
6. 전부다 y로 라이센스 내용 확인
7. `flutter doctor`로 해결되었는지 확인

## 빌드 이슈

### `flutter run` 혹은 `flutter build` 시 **android-sdk folder not writable** 관련 에러

에러에 표시되는 android-sdk 폴더로 가서 쓰기 권한 할당

### Dart SDK 혹은 flutter sdk 버전 문제

pub 패키지 저장소에서 다른 패키지를 사용할 때 flutter 버전이나 dart 버전의 요구사항이 달라서 문제가 되는경우,
보통 플러터 sdk 채널이 dev 채널로 되어있는 경우가 많음. 이 경우 stable/release 채널로 변경해야함

```bash
flutter channel # 업그레이드 채널이 무엇으로 되어있는지 확인
flutter channel stable # stable/release 채널로 변경, stable, beta, dev, master 총 3개
flutter upgrade # 자동으로 플러터와 dart sdk를 모두 변경함
```

## iOS 이슈

### Google Signin을 사용하려 할 때 뻗는 경우

iOS에서 `google_sign_in` 플러그인을 사용할 경우에는 반드시 공식 설명서에 써진대로 iOS `info.plist` 설정을 해야한다.

## 안드로이드 이슈

### `broken ANDROID_SDK_ROOT`와 유사한 이유로 에뮬레이터 실행이 안되는경우

#### 환경변수에 ANDROID_SDK_ROOT가 선언되어 있지 않는 경우

1. 안드로이드 스튜디오 메뉴에서 -> Tools -> sdk manager 선택
2. sdk manager화면에서 **Android SDK Location** 필드에 적힌 주소값 복사
3. 시스템 환경 설정의 환경 변수에 **ANDROID_SDK_ROOT** 추가 하고 2번에서 복사한 값으로 설정
4. 안드로이드 스튜디오 완전히 종료했다가 재실행
5. 에뮬레이터 실행

#### 환경변수에 ANDROID_SDK_ROOT가 선언되어 있는 경우

환경 변수에 **ANDROID_SDK_ROOT** 선언되어 있음에도 실행되지 않는 경우에는 아래의 순서를 따른다.

1. 환경 변수에서 **ANDROID_SDK_ROOT**를 완전히 제거
2. 안드로이드 스튜디오 완전히 종료 후 재실행
3. 에뮬레이터 실행 
4. 1, 2, 3을 거쳤음에도 실행되지 않을 경우 [환경변수에 ANDROID_SDK_ROOT를 정상적인 방법으로 추가](#환경변수에-ANDROID_SDK_ROOT가-선언되어-있지-않는-경우)


### 파이어베이스 연동시 Cannot fit requested classes in a single dex file 에러

1. `android/app/build.gradle`의 `minSdkVersion`을 21로 수정. 
2. 같은 파일의 `defaultConfig{}`에 `multiDexEnabled true` 추가.

더 이전의 sdk 버전을 사용하고 싶을 경우 [이 링크](https://developer.android.com/studio/build/multidex)를 

## 핫 리로드 이슈

### 핫 리로드를 수행했을 때 앱의 변화가 없는 경우 

1. `State`에 변화를 주는 코드가 추가된 경우 `hot restart`를 통해 `State`를 초기화하고 앱의 처음부터 시작해야 함. 
2. 위젯 트리가 변경된 경우, `hot restart`를 통해 앱의 처음부터 위젯 트리를 다시 생성해야 함. 
3. `pubspec.yaml`을 수정하고 `flutter package get`등을 통해 새로운 디펜던시나 에셋을 추가했을 경우, `stop`하고 다시 `run`해서 빌드부터 다시 해야함.
4. `iOS`나 `Android`의 메니페스트 파일 등을 수정했을 경우, `stop`하고 다시 `run`해서 빌드부터 다시 해야함. 

## Async 이슈

### Firebase 등의 서비스를 사용해서 async 작업을 할 때 foreground 프로세스의 성능이 저하되는 문제

Firebase 등의 stream 서비스를 이용하면 종종 해당 서비스를 async로 작업 하지 않고 응답이 끝나기를 기다려서 응답의 결과를 사용해야 하는 경우가 있다. 
이런 때에도 최대한 `await` 구문을 사용하지 않고 모두 `Future`나 `async`로 처리할 수 있는 루틴을 만들어야 한다. 

그렇지 않을 경우 foreground에서 view를 관리하는 작업과 중복되어 과부하로 인해 앱의 전체적인 성능이 저하될 우려가 있다. 

## Life Cycle

### dispose() 메소드의 중요성

`Stream` 에 대해서 listen을 하고 있는 경우 `dispose()` 메소드 내에서 제대로 `cancel` 해주지 않을 경우, 뷰가 없음에도 불구하고 계속 listen 하고 있는 문제가 발생함. 이로 인해 cpu stall/waste 및 memory leak이 발생함. 이와 같은 async 형태의 Stream은 반드시 dispose에서 cancel 해야함. 

`Stream`을 사용하는 경우에는 `life cycle`에 따라서 코드의 위치에 따라 중복 `listen`이 될 수 있으므로 이를 잘 고려해서 코드를 구성해야함. 
