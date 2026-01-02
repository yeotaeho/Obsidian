
카카오 
네이버
구글

![[Pasted image 20251126110925.png]]

1. 사용자가 "카카오톡 계정으로 로그인" 클릭
2. 프론트엔드 → GET /api/user/kakao/login
3. 백엔드 → 카카오 인증 URL 반환
4. 프론트엔드 → 카카오 로그인 페이지로 리다이렉트
5. 사용자가 카카오 계정으로 로그인
6. 카카오 → http://localhost:3000/auth/kakao/callback?code=xxx 로 리다이렉트
7. 프론트엔드 콜백 페이지 → POST /api/user/kakao/callback (code 전달)
8. 백엔드 → 카카오 토큰 발급 + 사용자 정보 조회 → 응답
9. 프론트엔드 → 로그인 처리 후 대시보드로 이동

![[Pasted image 20251128095220.png]]
5번에서 사용자가 카카오서버에서 로그인을 함으로서 인증이 된다 그로인해 권한을 부여 해주는 코드을 즉 인가 코드을 준다

![[Pasted image 20251128095228.png]]




authentication 인증 리플래시토큰

authorization 권한  인가코드  액세스토큰

Redirect

토큰

pkce

jwt
jwt 구조
![[Pasted image 20251126120027.png]]


머리 header
바디 Payload


디코딩 

인코딩

![[Pasted image 20251126115003.png]]

Payload 택배내용물
claims 송장