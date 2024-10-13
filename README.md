## 왜 또 다른 슈퍼유저가 필요한가요?
* 슈퍼유저는 오픈 소스여야 합니다. 그것은 당신의 기기에서 루트를 위한 관문입니다. 그것은 독립적인 보안 분석을 위해 열려 있어야 합니다. 모호함(폐쇄형 소스)은 보안이 아닙니다.
* 슈퍼유저는 NDK 빌드 가능해야 합니다. 내부 안드로이드 참조 없음.
* 슈퍼유저는 ROM에 내장하고 싶어하는 사람들을 위해 AOSP를 빌드할 수 있어야 합니다.
* 슈퍼유저는 AOSP _임베디드가_ 가능해야 합니다. 즉, ROM이 설정 앱에 쉽게 임베드할 수 있어야 합니다.
* 마켓과 소스 저장소 모두에 대한 유지관리와 업데이트는 시기적절하게 이루어져야 합니다.
* 내 앱 사용자에게 내가 작성한 슈퍼유저 솔루션을 알려주고 싶습니다. 제가 아는 건 효과가 있고, 뭔가 문제가 있으면 고칠 수 있다는 거예요.
* 멀티유저(4.2+)를 적절히 처리하세요
* 동시 SU 요청을 적절히 처리합니다.

## 번역

번역은 언제나 환영이지만, GitHub에 번역본을 제출하지는 말아주세요! 대신, 리뷰 제출 프로세스를 사용하세요 [CyanogenMod의 게릿 인스턴스](http://review.cyanogenmod.org/#/q/status:open,n,z).

## 소스 확인

"위젯" 종속성이 필요합니다.

```bash
$ mkdir /path/to/src
$ cd /path/to/src
$ git clone git://github.com/koush/Superuser
$ git clone git://github.com/koush/Widgets
```

이러한 저장소는 최상위 디렉토리에 실제 프로젝트를 보관하지 않습니다.
이는 테스트, 라이브러리, 샘플을 포함하고 있기 때문입니다.

Android SDK 관리자를 통해 API 19용 SDK 플랫폼이 설치되어 있는지 확인하세요.  NDK Revision 9b를 설치하세요 [여기](http://developer.android.com/tools/sdk/ndk/index.html).

## Eclipse

식에서 위젯/위젯 및 슈퍼유저와 슈퍼유저를 가져옵니다. 그냥 작동해야 합니다(TM).

## Ant

```bash
$ mkdir /path/to/src
$ cd /path/to/src
$ cd Superuser/Superuser
```

이 디렉토리에 local.properties라는 파일을 만듭니다. 이 파일은 ant에서 사용자 정의 속성에 사용됩니다. NDK 디렉토리 위치와 키스토어 매개변수를 지정해야 합니다.

```
ndk.dir=/Users/koush/src/android-ndk
key.store=/Users/koush/.keystore
key.alias=mykey
```

아직 릴리스 키가 없는 경우, [KeyTool을 사용하여 하나 생성](http://developer.android.com/tools/publishing/app-signing.html).

SDK 경로 설정 (플랫폼 도구, 도구 등이 들어 있는 디렉토리입니다):

```bash
$ export ANDROID_HOME=/Users/koush/src/sdk
```
그러면 다음을 빌드할 수 있습니다.
```bash
$ ant release
```

출력:
* bin/update.zip - 복구 설치 가능 ZIP
* bin/Superuser-release.apk - 슈퍼유저 안드로이드 앱
* libs/armeabi/su - ARM 슈퍼유저 바이너리
* libs/x86/su - x86 슈퍼유저 바이너리
* libs/mips/su - MIPS 슈퍼유저 바이너리

## 슈퍼유저 바이너리 빌드

위에 표시된 대로 Ant를 사용하여 바이너리를 빌드할 수 있지만, APK를 빌드하지 않고도 빌드할 수 있습니다.

경로에 "NDK 빌드" 도구를 사용하여 Android NDK를 다운로드했는지 확인하세요.

```bash
$ cd /path/to/src/
$ cd Superuser/Superuser
$ ndk-build
```

Superuser 바이너리는 Superuser/Superuser/libs/armeabi/su에 빌드됩니다.

## AOSP, CyanogenMod 등을 사용하여 빌드

ROM 개발자는 내가 공개한 공식 슈퍼유저 APK와 바이너리를 배포할 수 있습니다. 그러면그러면
Google Play에서 업데이트를 받을 수 있습니다. 원하면 건설해도 됩니다.

빌드에 슈퍼유저를 포함하는 방법은 두 가지가 있습니다. 가장 쉬운 방법은 APK를 별도의 앱으로 빌드하는 것입니다.
그렇게 하려면 아래에 설명된 대로 local_manifest.xml을 추가하기만 하면 됩니다. 두 번째 방법은 임베드하는 것입니다.
안드로이드 기본 시스템 설정으로 들어갑니다.

#### 리포 설정
[local_manifest.xml](https://github.com/koush/Superuser/blob/master/local_manifest.xml)을 .repo/local_manifests에 추가합니다.

#### 패키지 이름 구성
Google Play에서 배포되는 Superuser의 패키지 이름은 com.koushikdutta.superuser입니다.
Play 스토어 버전과의 충돌을 방지하기 위해 빌드 프로세스는 패키지를 변경합니다.
이름을 com.thirdparty.superuser로 지정합니다. 다음을 설정하여 이 값을 구성할 수 있습니다. 공급업체 Makefile 또는 BoardConfig에서:

```
SUPERUSER_PACKAGE := com.mypackagename.superuser
```

#### 고급 - 시스템 설정에 슈퍼유저 포함

위에서 설명한 대로 패키지 이름을 변경할 필요는 없습니다. 슈퍼유저는 간단히 
com.android.settings 패키지로 이동합니다.

먼저, 제품 makefile(vendor/cm/config/common.mk 등)에서 다음을 지정하세요.

```
SUPERUSER_EMBEDDED := true
```

패키지/앱/설정을 수정하려면 이 [패치](http://review.cyanogenmod.org/#/c/32957/2//COMMIT_MSG,unified)가 필요합니다.
패치는 단순히 external/koush에 체크아웃된 소스를 참조하고 XML 환경 설정을 변경합니다. 파일과 AndroidManifest.xml. 아주 최소한의 변경입니다.
