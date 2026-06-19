# BOM(Byte Order Mark) — CSV에서 자주 만나는 버그

CSV 파일을 다룰 때 종종 마주치는 버그가 **BOM(Byte Order Mark)** 입니다.

정상적인 데이터는 다음과 같습니다.

```
name,age,city
taeho,25,seoul
```

그런데 BOM이 끼면 맨 앞에 `ï»¿` 같은 문자가 붙어 버립니다.

```
ï»¿name,age,city
taeho,25,seoul
```

이렇게 `ï»¿`가 첫 컬럼명 앞에 붙으면, 첫 번째 헤더(`name`)를 제대로 인식하지 못해 파싱 단계에서 문제가 생깁니다.
