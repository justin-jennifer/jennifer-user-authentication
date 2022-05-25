# 제니퍼 사용자 인증 활용하기

제니퍼 화면 및 대시보드 Iframe 연동 샘플이다. 고객사 통합 대시보드에 제니퍼 대시보드를 공유하기 URL을 사용하여 Iframe으로 보여주고 있는데, 2개 이상의 Iframe을 사용하거나 제니퍼의 다른 화면으로 이동이 제한되는 문제가 있다.

본 샘플에서 다룰 내용은 다음과 같음

 - URL 쿼리 스트링으로 제니퍼 인증하는 SSO 어댑터 구현하기
 - SSO 어댑터를 활용한 제니퍼 화면 열기
 - SSO 어댑터를 활용한 제니퍼 대시보드 Iframe으로 호출하기

## 제니퍼 뷰서버 설정

 1. 크롬의 Iframe 보안 정책으로 인해 제니퍼 뷰서버는 HTTPS로 실행해야 함
 2. server_view.conf -> 'add_same_site_none_cookie = true' 추가
 3. 설정 -> 어댑터 및 플러그인에 'url-sso_jennifer-1.0.0.jar' 추가


![다운로드](https://user-images.githubusercontent.com/1277117/169983899-83c00ad9-da4e-4aff-bae1-d7c6d75df6e6.png)

## 샘플 대시보드 실행

프로젝트를 체크아웃 받은 후, 해당 디렉토리 안에서 다음과 같은 명령어를 실행해야 함

```shell
npm install -g serve
serve -s build
```

## SSOLoginAdapter 클래스

본 샘플에서는 URL 쿼리스트링으로 제니퍼5 사용자의 아이디와 비밀번호를 받아오지만 상황에 따라 HTTP 요청 헤더나 어댑터 옵션을 활용해도 되는데, 구현 방식은 고객사 상황에 맞게 정하면 된다.
```java
public class SSOLoginAdapter implements SSOLoginHandler {
    public UserData preHandle(HttpServletRequest request) {
        String id = request.getParameter("id");
        String password = request.getParameter("password");

        if (id != null && password != null) {
            LogUtil.info("Logging in with querystring : " + id + "," + password);
            return new UserData(id, password);
        }
        
        return null;
    }
}
```

## 제니퍼5 URL로 인증하기

SSO 로그인 어댑터를 제니퍼 설정에 추가했다면 다음과 같은 URL로 사용자 인증을 브라우저 주소창을 통해 시도할 수 있다. 참고로 아래 URL은 실제로 동작하며, 테스트를 위해 이미 환경을 구성했다. 아래 사용자 정보는 실제로 사용할 수 있으며, 본 샘플을 위해 사전에 생성해두었다.

```shell
https://support.jennifersoft.com:7979/login/sso?id=iframe&password=1234
```

사용자 인증이 완료되면 이미 설정된 시작화면으로 리다이렉트 되지만 다음과 같이 임의로 지정할 수 도 있다.

```shell
https://support.jennifersoft.com:7979/login/sso?id=iframe&password=1234&redirect=%2Fpopup%2FxviewAnalysis%3FdomainId%3D3000%26transactionId%3D-3401872944142564948%26searchTime%3D1653381721418%26redirect%3D%252Fpopup%252FxviewAnalysis%253FdomainId%253D3000%2526transactionId%253D-3401872944142564948%2526searchTime%253D1653381721418
```

