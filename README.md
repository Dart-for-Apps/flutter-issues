# 각종 이슈 정리

- [설치 이슈](#설치-이슈)
- [빌드 이슈](#빌드-이슈)
- [안드로이드 이슈](#안드로이드-이슈)

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

## 안드로이드 이슈

### `broken ANDROID_SDK_ROOT`와 유사한 이유로 에뮬레이터 실행이 안되는경우

1. 안드로이드 스튜디오 메뉴에서 -> Tools -> sdk manager 선택
2. sdk manager화면에서 **Android SDK Location** 필드에 적힌 주소값 복사
3. 시스템 환경 설정의 환경 변수에 **ANDROID_SDK_ROOT** 추가 하고 2번에서 복사한 값으로 설정
4. 안드로이드 스튜디오 완전히 종료했다가 재실해
5. 에뮬레이터 
