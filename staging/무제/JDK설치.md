# JDK 설치 — 다운로드부터 환경 변수 설정까지

자바 개발 환경의 기본인 JDK를 설치하고 환경 변수를 설정하는 과정을 정리했습니다.

## 1. 다운로드

- 설치 안내 참고: https://lite.evernote.com/note/b614199e-fb84-df09-be8c-35b4eeb9c632
- JDK 다운로드: https://www.oracle.com/java/technologies/downloads/#jdk21-windows

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930125644.png]]
가운데 항목을 다운로드한 뒤 설치합니다. 설치 경로는 `내 PC > 로컬 디스크 > Program Files > Java`입니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930125920.png]]
이렇게 뜨면 정상입니다.

## 2. 환경 변수 설정

`Window + R`을 누르고 다음을 입력합니다.

```
sysdm.cpl ,3
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930130219.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930130241.png]]
**환경 변수**를 클릭합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930153437.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930153508.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930153527.png]]
<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930153537.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930153549.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930153557.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20250930153603.png]]

설치 흐름은 이후 Node.js나 아나콘다 설치와도 거의 같으므로, 이 과정을 익혀 두면 다른 도구 설치에도 그대로 응용할 수 있습니다.
