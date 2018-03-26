# 설치 및 시작

모바일 앱 개발시 필요한 플랫폼 중 하나로 구글에서 새로 나온 `flutter `가 있다. 

다트기반으로 개발한다. 그럼 설치 부터 하나씩 해보자.

윈도우 기반으로 해보겠다. 

[https://flutter.io/get-started/install/](https://flutter.io/get-started/install/)

우선 Git을 통해 flutter sdk 를 받아야 한다. 

```
git clone -b beta https://github.com/flutter/flutter.git
```

그리고 flutter 폴더에 가서 flutter/bin 까지 path를 환경변수로 등록하자. 

![](/assets/flutter1-1.png)

그리고 콘솔을 다시 실행해서 flutter doctor 를 실행해보자. 

```
flutter doctor
```

현재 시스템에 flutter 상태를 보여주는 명령어이다. Android 설치 툴이나 그런걸 요약해서 보여준다. 

![](/assets/flutter1-2.png)

위 사진에선 2.3~3.1까지 설치가 되어있지만 Intellij, VCode 에선 따로 플러그인이 설치가 안되어 있는 상태이다. 

Android Stuido 가 설치가 되어있다고 가정하고 진행한다. 아직 설치를 못한 경우 아래의 링크에서 설치를 하고 넘어가자. 

[https://developer.android.com/studio/index.html](https://developer.android.com/studio/index.html)

---

Android 디바이스와 연결 및 디버깅하기

device 확인 명령어

```
flutter devices
```

![](/assets/flutter1-3.png)

```
flutter run
```

> 만약 Android SDK 가 다른 곳에 깔린 경우 설치 주소를 ANDROID\_HOME 을 환경설정에 Path 로 넣어주자.

# Set up the Android emulator

To prepare to run and test your Flutter app on the Android emulator, follow these steps:

* Enable VM acceleration on your machine.
* Launch Android Studio&gt;Tools&gt;Android&gt;AVD Manager and select Create Virtual Device.
* Choose a device definition and select Next.
* Select one or more system images for the Android versions you want to emulate, and select Next. An x86 or x86\_64 image is recommended.
* Under Emulated Performance, select Hardware - GLES 2.0 to enable hardware acceleration.
* Verify the AVD configuration is correct, and select Finish.

For details on the above steps, see Managing AVDs.

In Android Virtual Device Manager, click Run in the toolbar. The emulator starts up and displays the default canvas for your selected OS version and device.

Start your app by running flutter run. The connected device name is Android SDK built for &lt;platform&gt;, where platform is the chip family, such as x86





















