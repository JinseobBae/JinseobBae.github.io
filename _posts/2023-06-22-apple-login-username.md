---
layout: post
title: "애플 로그인 시 사용자명 문제"
categories: [java, spring, apple]
tags: [java, spring, apple]
---

애플 로그인을 구현 중 사용자 프로필을 조회하는데 아무리 찾아봐도 사용자명이 없었다. 

공식문서를 보니 사용자 정보인 id_token에도 이름을 안준다고 되어 있다.

> Apple doesn’t receive the user’s full name shared with the system UI. The raw data is passed directly to your app from the browser and is not included in the user’s identity token. To help prevent cross-site scripting attacks, validate and sanitize the user-submitted first and last name values before storing on your app servers.


이리저리 검색하다 보니 처음 인증 요청 할 때 scope에 name을 추가하면 된다고 한다. 

```
https://appleid.apple.com/auth/authorize?
response_type=code
&client_id=클라이언트_ID
&scope=name+email
&response_mode=form_post
&redirect_uri=인증 성공 후 호출 할 uri
```

아마 이런 형태일듯하다.

실제로 저렇게 해서 호출 한 경우 redirect_uri로 아래와 같이 user 값이 전송된다.

```JSON
{
   "user":{
      "name":{
         "lastName":"이름",
         "lastName":"성"
      },
      "email":"aaaa@google.com"
   }
}
```

<span style="color:red">단, 사용자명은 최초 연동에 한해 **딱 한번만** 전송해준다. </span>

이후 해당 계정으로 연동 시도 시 사용자명은 전달해주지 않는다.

만약 다시 전달 받으려면 애플계정으로 가서 해당 연동을 해제해야 한다. 

해제는 애플 기기에서 [설정 - 이름 클릭 - 암호 및 보안 - Apple로 로그인 - 셀렙샵 - Apple ID 사용 중단] 으로 하면 된다. 



참고

- [https://developer.apple.com/documentation](https://developer.apple.com/documentation/sign_in_with_apple/sign_in_with_apple_rest_api/authenticating_users_with_sign_in_with_apple)
- [https://developer.apple.com/forums](https://developer.apple.com/forums/thread/118209?page=4)
