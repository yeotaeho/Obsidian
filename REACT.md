
node.js 설치 
https://nodejs.org/en/download
![[Pasted image 20251107115926.png]]
에서 windows installer(.msl) 클릭 후 다운로드


 







![[Pasted image 20251107120209.png]]
![[Pasted image 20251107120228.png]]
이 경로로 들어가서 경로주소 복사 


![[Pasted image 20251107120017.png]]
들어가서


![[Pasted image 20251107120057.png]]
환경변수 클릭 

![[Pasted image 20251107120116.png]]
에서 시스템 변수에 새로만들기 클릭

![[Pasted image 20251107120431.png]]
추가해주고
이렇게 추가해야지 터미널에서 버전이 뜸 node --version

![[Pasted image 20251126173806.png]]

path에서도 

![[Pasted image 20251126173827.png]]

![[Pasted image 20251126173844.png]]
을 만들어줘야함

![[Pasted image 20251107122830.png]]
파워셀에 `npm --version`을 치면 이렇게 화면이 뜰 것이다


파워셀을 관리자도 다시 실행하고 
`Set-ExecutionPolicy RemoteSigned`
치고 y을 누르면 해결

아토믹패턴
![[Pasted image 20251110125322.png]]

모든 것은 컴포넌트(=객체)다
{}(노테이션)으로 감싸져 있으면 객체 다 리액트

상태에따라 객체인지 아닌지 판단 값이 들어간상태 들어가지 않으면 생성되지않음

컴포넌트 - 클래스
 function a (){} - class a{}

html dom = new html
![[Pasted image 20251110123051.png]]


npm install -g pnpm
pnpm install

npm대신 pnpm을 쓴다



`function Button({ label, onClick }) { return <button onClick={onClick}>{label}</button>;}` 

`function App() { return <Button label="클릭" onClick={() => alert("Hi")} />; }`

프론트에서 axios을 통해 값을 보내면 백에서 crossorigin을 통해 값을 받는다

# 무상태

프론트가 보안이 취약함 -> 이러한 문제을 해결하기위해 무상태을 적용
즉 상태 들을 함수(function)가 가지고 있지 않게 만드는거임 그럼 해커들 입장에서도
들여다 봤을때 기능만 보이게됨 중요한 정보는 안보임

이렇게 하는 방법은 함수안에 변수(정보 , status)을 설정안하면됨 

위 이미지 처럼 인터페이스로 


next.js + react 

인터페이스
zustand

provider
스토어와 리액트을 연결하는 객체
# state
![[Pasted image 20251113094611.png]]
# status
![[Pasted image 20251113094620.png]]
둘다 상태을 뜻함

state컴포넌트
status CRUD

도커 컨테이너 만드릭


넥스트을 조작하면 자동으로 리액트가 봐뀜


page가 컴포넌트 함수

컴포넌트 안에선 state 밖에선 prop 나중에 서버쪽으로 보낼때 prop으로 보내야함 둘다 상태임
state는 한방향으로 흐르고 prop는 양방향



리액트,tsx,리덕스

리덕스 :상태관리자
상태을 관리하고 봐꾸는 역할 스프링 컨텍스트와 연결

npx create-next-app@14 my-app --ts

메모제이션-메멘토
callback



tsx 구조
컴포넌트  
 +
컨테이너 

사용자가 액션을 하면 보내면 컨테이너에서 핸들링으로 컴포넌트로 보내고 컴포넌트에서 use즉 훅을 이용해서 상태을 변환 후 리턴 다시 컨테이너로 와서 화면으로 보임 
컨테이너-->컴포넌트-->컨테이너

![[Pasted image 20251124095929.png]]

![[Pasted image 20251124095942.png]]

![[Pasted image 20251124095951.png]]

컨텍스트을 스토어(애플리케이션 전체의 상태를 한 곳에서 관리하는 객체)라고 생각하면 편함

스토어관리 라이브러리
리덕스,zustand




@frontend 여기에 컨텍스트로 store을 반드시 1개만 만들고, 이것을 관리하는 라이브러리로 
zustand을 사용하려고해 , 해당 과정에 대해 전략을 작성해줘.

app router

deprecated




# 미들웨어

![[Pasted image 20251121130940.png]]

중간에서 데이터 흐름을 관리한다
**미들웨어 = 시스템과 시스템 사이에서 공통 기능을 제공해주는 중간 관리자**  
**요청/응답 흐름을 가로채고 처리한 뒤 다음 단계로 넘기는 기술**
DB에서 바로 프론트로 넘어갈수있음 
바로 넘겨도되는 데이터(광고)은 바로 하면으로 나갈수있게 할수있다 

언제든지 설치할수있고 없앨수있다


레디스(초고속 메모리 기반 데이터 저장소(In-memory DB))
또 하나의 db 근데 postgre같은 중요한 정보을 담는게 아니라 바로화면으로 보내도 될 만큼에 정보만 저장 또한 캐시 즉 일정기한이 지나면 지워질것들을 저장



# 스토어

**애플리케이션 전체의 상태를 한 곳에서 관리하는 객체**
슬라이스 값이 공유되는 자바에선 컨트롤러에서 값을 전달할려면 파이프라인을 만들어줘야 하는데 스토어 슬라이스는 그런 개념이 아님 어느 한슬라이스에서 값을 만드면 모든 슬라이스에 공유가능

상태을저장

파편화 되어있는 컨텍스트을 하나로 

방향이 없음 자유롭게

사용자가 화면에서 액션을 취하면 그 액션을 감지하고 스토어로 제이슨형태로 상태가 저장됨
스토어에서 빠르게 

리액트에서 
함수 훅과 핸들러
훅은 use로시작하고 라이브러리로 받아오는 컴포넌트에서 사용함
![[Pasted image 20251120110529.png]]

훅 스토어랑 연결

핸들러는 두가지 종류로 나눌수있다
핸들러는 사용자의 행동(이벤트)에 반응해서 실행되는 함수
핸들러는 컨테이너에서 정의하고, UI 컴포넌트에 콜백으로 전달하는 함수이다.
![[Pasted image 20251128113524.png]]

tsx파일은 반드시 훅과 return 즉 컨테이너가 있어야한다
ts파일은 핸들러만 이벤트에대한 기능들만 
tsx파일과 ts파일을 따로 쪼개서 기능과 속성을 따로 구분 서로연결시키는 함수가 훅
ts파일은 - JSX 문법 (<div>, <Component /> 등),- React 컴포넌트 은 못들어감


JSX는 **JavaScript + XML** 형태의 문법으로, **React에서 UI를 표현하기 위해 사용하는 특별한 문법**
자바스크립트 안에서 HTML처럼 생긴 코드를 작성할 수 있게 해주는 문법



![[Pasted image 20251124095402.png]]



node.js
구조분해
클라이언트랑 서버
익스프레스 
라우팅과 미들웨어


undefind버려라
null버리지마라

?:
옵셔널

?.
??


(...a)
# next.js
서버 사이드 랜더링
ssr
클

csr


ssr동기 전화

csr비동기 문자 

삼항연산자
![[Pasted image 20251120123704.png]]

앱라우팅


# 클로저

울타리
![[Pasted image 20251128114144.png]]

![[Pasted image 20251128114135.png]]
이부분이 클로저 

**이너함수**
![[Pasted image 20251128112644.png]]
![[Pasted image 20251128113257.png]]




클로저는 한 컨넥스트로 묶는다
![[Pasted image 20251128113205.png]]





# iife
즉시 실행 함수

상시대기중인 함수을 뜻함

사용자가 이벤트을 할때 실행되는 함수가 아닌

항상 실행되있는 함수
모니터링

![[Pasted image 20251128120507.png]]

위에 코드는 호출해야지 실행 되지만 
밑에 코드는 호출없이 항상 실행되이씅ㅁ


무상태 