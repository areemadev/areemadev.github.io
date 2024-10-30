---
title: "[ReactNative] iOS Fastlane 으로 TestFlight 에 업로드 하기"
categories: dev
tags:
  - react-native
  - fastlane
toc: true
toc_sticky: true
---

### fastlane 설치

**fastlane** 은 Ruby 로 만들어져 있기 때문에 Ruby 를 먼저 설치 해야한다.
Ruby 는 직접 설치도 가능하지만 버전 관리를 위해서 rbevn 를 사용해 설치하도록 한다.

> fastlane은 Ruby 버전 2.5 이상을 지원합니다. 사용 중인 Ruby 버전을 확인하세요:

<a href="https://github.com/rbenv/rbenv" target="_blank">rbenv 설치하기</a>

Ruby 가 설치 되었다면 bundler 도 설치한다.

```
gem install bundler
```

ios 폴더에 `.Gemfile` 을 생성하고 아래 스크립트를 추가한다.

```
source "https://rubygems.org"

gem "fastlane"
```

그리고 `bundle install` 을 실행해서 fastlane 을 설치한다.

### 인증 관련 처리

인증은 **match** 를 사용한다. match 는 **git repository** 에 인증서 및 프로비저닝 프로필을 올려두고, 여러 사람들이 공동으로 코드 사이닝을 가능하게 해준다. 원래 **fastlane** 적용하다 보면 코드 사이닝 관련해서 굉장히 귀찮은 작업을 해줘야 하는데 **match** 를 사용하면 그런 번거로움이 줄어든다.

사용법도 간단하다. 아래 명령어로 **match** 를 초기화 시켜준다.

`fastlane match init`

그러면 아래처럼 **git repository** 주소를 입력하라는 커맨트가 나오는데 프로젝트의 git repo 를 입력해주면 된다.

![](https://docs.fastlane.tools/img/actions/match_init.gif)

> 단 주의할 점은 반드시 git repo 를 private 상태로 만들어주고 진행해야 한다는 점이다. (안그러면 털림)

이게 끝이다. 완료되고 나면 **ios** 폴더내에 **Matchfile** 이라는 파일이 생성 되어 있을 것이다.

```
git_url("https://github.com/fastlane/certificates") app_identifier("tools.fastlane.app")

storage_mode("git)

type("devlopment")
```

대략 이런 내용이 생성되어 있을 건데 굳이 수정할 필요는 없다. 자세한 내용을 알고 싶다면 아래 링크를 참고하자.

<a href="https://docs.fastlane.tools/actions/match" target="_blank">https://docs.fastlane.tools/actions/match</a>

**match**로 인증을 하기 위해 **Fastfile** 파일 내 **build_app** 위에 `match(type: "appstore")` 를 추가해 준다.

```
platform :ios do

	desc "Push a new beta build to TestFlight"

	lane :beta do

		match(type: "appstore") <--- 여기

		increment_build_number(xcodeproj: "awesome_project.xcodeproj")

		build_app(workspace: "awesome_project.xcworkspace", scheme: "awesome_project")

		upload_to_testflight

	end

end
```

### 앱스토어 인증

앱스토어 인증 방법은 몇가지가 있다. 문서에는 App Store Connect API Key 를 이용하는걸 추천 하지만, 귀찮으니 여기서는 2단계 인증을 사용한다. 딱히 어렵지 않기 때문이다.

아래 명령어를 사용하면 2단계 인증을 진행하고 인증 결과를 Cookie 에 저장해 놓는다.

```
fastlane spaceauth -u name@gmail.com
```

2단계 인증은 이게 끝이다. 그리고 한가지 더 인증을 해줘야 하는데 _Application-specific passwords_ 라는 것이 있다. 쉽게 말하면 애플 개발자 계정에서 관리해주는 private key 라고 보면 된다.

<a href="https://appleid.apple.com" target="_blank">https://appleid.apple.com</a> 로 이동해서 App 보안암호를 생성 한다. 생성한 암호는 캡쳐를 해놓거나 클립보드에 복사를 해놓는다.

fastlane 에서 해당 암호를 통해 인증 받기 위해서는 환경변수에 값을 넣어줘야 한다.

환경변수를 사용하기 위해 dotenv 를 사용한다. ios 폴더 내에서 아래 명령어로 dotenv 를 설치한다.

```
sudo gem install dotenv
```

설치가 완료되면 .env 파일 생성 한다. 그리고 아래 환경변수를 추가해 준다.

```
FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD=#보안 암호
```

### TestFlight 에 업로드

이제 모든 준비가 끝났다. 아래 명령어로 TestFlight 에 업로드 하도록 하자.

```
fastlane beta
```

### 참고

<a href="https://docs.fastlane.tools/getting-started/ios/setup/#" target="_blank">https://docs.fastlane.tools/getting-started/ios/setup/#</a>
