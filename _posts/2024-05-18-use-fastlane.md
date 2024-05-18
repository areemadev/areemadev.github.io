---
title: "[ReactNative] Fastlane iOS 적용 방법"
tags:
  - react-native
  - fastlane
layout: post
---

### fastlane 설치

fastlane 은 Ruby 로 만들어져 있기 때문에 Ruby 를 먼저 설치 해야한다.
Ruby 는 직접 설치도 가능하지만 버전 관리를 위해서 rbevn 를 사용해 설치하도록 한다.

https://github.com/rbenv/rbenv

> fastlane은 Ruby 버전 2.5 이상을 지원합니다. 사용 중인 Ruby 버전을 확인하세요:


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

인증은 match 를 사용한다.

`fastlane match init`

TwoFactor Authentication

fastlane spaceauth -u name@gmail.com

dotenv 설치

```
sudo gem install dotenv
```

https://appleid.apple.com
위 사이트로 이동해서 app 보안암호를 생성

아래 것들을 환경변수에 추가

.env 파일 생성

아래 환경변수 추가

FASTLANE_APPLE_APPLICATION_SPECIFIC_PASSWORD=#보안 암호

Fastfile 에 build_app 위에 match(type: "appstore") 를 추가

```
platform :ios do

	desc "Push a new beta build to TestFlight"

	lane :beta do

		match(type: "appstore")

		increment_build_number(xcodeproj: "directcloud_mobile_rn.xcodeproj")

		build_app(workspace: "directcloud_mobile_rn.xcworkspace", scheme: "directcloud_mobile_rn")

		upload_to_testflight

	end

end
```
