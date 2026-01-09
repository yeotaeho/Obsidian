
aws에서 
루트 사용자
iam 사용자 



rds - db

ecc   server


sss  디스크
버킷 - 컨테이너

아마존이랑 연동하는 과정이
로컬(내 pc)->git->aws로 git(중재자) 역활이 중요함 

내pc에서 git으로 갈때는 그냥 push하면 되지만 git에서 aws로 갈려면 깃 액션을 따로 설정해줘야함
![[Pasted image 20251219131506.png]]
자기 프로젝트을 깃한테 명령줘야함 아마존에 연결 




![[Pasted image 20251218154906.png]]

![[Pasted image 20251218154952.png]]
 에서 instances클릭
 ![[Pasted image 20251218155313.png]]
 이름 작성하고

![[Pasted image 20251218155500.png]]
여기서 뭘 선택하면 될지 나의 프로젝트 기술에 따라 gpt한테 물어봐봐

![[Pasted image 20251218155519.png]]


![[Pasted image 20251218160432.png]]
여기 부분도도

![[Pasted image 20251218160453.png]]

![[Pasted image 20251218160500.png]]


![[Pasted image 20251218160508.png]]

옆에 create new key pair 클릭

![[Pasted image 20251218160558.png]]

이렇게 나오는데 이것도 물어봐 

![[Pasted image 20251218160741.png]]

다 물어봐

![[Pasted image 20251218163109.png]]

![[Pasted image 20251218163220.png]]

![[Pasted image 20251218163352.png]]

![[Pasted image 20251218163604.png]]
![[Pasted image 20251218164015.png]]

![[Pasted image 20251218164302.png]]

![[Pasted image 20251218164057.png]]

![[Pasted image 20251218164413.png]]

![[Pasted image 20251218164802.png]]

![[Pasted image 20251218164750.png]]

![[Pasted image 20251218165205.png]]


![[Pasted image 20251218165533.png]]

![[Pasted image 20251218165909.png]]

![[Pasted image 20251218165943.png]]

![[Pasted image 20251218170242.png]]


![[Pasted image 20251218170413.png]]


![[Pasted image 20251218171736.png]]

# git action

이 fastapi을 연결된 ec2 에 github action을 통해 cicd 방식으로 배포하도록 전략을 알려줘

aws ec2와 연결 후 (서버 폴더) 이것을 깃허브 액션 cicd로 연동하는 코드을 작성해줘

하고 Secret설정 해야함
![[Pasted image 20251218175455.png]]

자기 올린 프로젝트에 설정탭

![[Pasted image 20251218175258.png]]

![[Pasted image 20251218175305.png]]

![[Pasted image 20251218175639.png]]

![[Pasted image 20251218180049.png]]

![[Pasted image 20251218180126.png]]
이 값들은 커서한테 물어봐 커서가 작업했으니까


![[Pasted image 20251218181204.png]]
이렇게 
![[Pasted image 20251218181213.png]]
커서 내용 참고해서
![[Pasted image 20251219102419.png]]

4번의 값이 호스트임 EC2_HOST

일단 깃 허브 액션까지 했고 CICD로 배포해야함



우분투 즉 아마존에 올릴려면 
![[Pasted image 20251219111522.png]]
올릴 폴더과 똑같은구조로 우분투에도 설정해야함 만약 api 즉 백서버을 올리려고 하면 백서버 에 폴더 파일구조와 똑같이 맞춰야함
![[Pasted image 20251219111938.png]]
폴더만들때 mkdir (이름)
파일만들때 touch (이름)
![[Pasted image 20251219113524.png]]
파일 편집할때 명령어 우리는 vim써야함

![[Pasted image 20251219113750.png]]
들어가면 이런화면인데 

![[Pasted image 20251219113710.png]]
이게 나가는 명령어


![[Pasted image 20251219112903.png]]


![[Pasted image 20251218182129.png]]



![[Pasted image 20251219154117.png]]

![[Pasted image 20251219154126.png]]

api 폴더 전체 업로드 (model_weights 제외)
scp -i "Dovahkiin.pem" -r api/* ubuntu@ec2-3-39-230-121.ap-northeast-2.compute.amazonaws.com:~/api/

또는 rsync 사용 (더 효율적)
rsync가 설치되어 있다면
rsync -avz --exclude 'model_weights' --exclude '__pycache__' --exclude '*.pyc' --exclude 'venv' -e "ssh -i Dovahkiin.pem" api/ ubuntu@ec2-3-39-230-121.ap-northeast-2.compute.amazonaws.com:~/api/

![[Pasted image 20251219154133.png]]cd ~
api 폴더만 클론하는 것은 어려우므로, 전체 레포 클론 후 api만 사용
git clone https://github.com/yeotaeho/learn_4.git temp_repo
cp -r temp_repo/api ~/api
rm -rf temp_repo

![[Pasted image 20251219184142.png]]

![[Pasted image 20251219184155.png]]

![[Pasted image 20251219190026.png]]



![[Pasted image 20251222110948.png]]
os이미지
윈도우
머신이미지
![[Pasted image 20251222114327.png]]


# SSS

![[Pasted image 20251229160240.png]]


![[Pasted image 20251229160252.png]]

![[Pasted image 20251229165205.png]]
여기에 있는 세팅들 다 뭐 알아서 알아보고 설정 

![[Pasted image 20251229165258.png]]

![[Pasted image 20251229165354.png]]

![[Pasted image 20251229165359.png]]

[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "PUT",
            "POST",
            "DELETE"
        ],
        "AllowedOrigins": [
            "http://yeotaeho.kr"
        ],
        "ExposeHeaders": [
            "ETag"
        ],
        "MaxAgeSeconds": 3600
    }
]



자기 도메인 에 맞춰서 설정

![[Pasted image 20251229165438.png]]

![[Pasted image 20251229165447.png]]


![[Pasted image 20251229170553.png]]

![[Pasted image 20251229170800.png]]

![[Pasted image 20251229170917.png]]

![[Pasted image 20251229170953.png]]

![[Pasted image 20251229171107.png]]

![[Pasted image 20251229171403.png]]

{ "Version": "2012-10-17", "Statement": [ { "Effect": "Allow", "Action": [ "s3:GetObject", "s3:PutObject", "s3:DeleteObject" ], "Resource": "arn:aws:s3:::yeotaeho/*" } ] }

![[Pasted image 20251229173428.png]]


![[Pasted image 20251229173819.png]]

![[Pasted image 20251229174148.png]]

기존 git action -> aws
소스을 올려서 실행 

git action -> docker hub -> aws
도커 이미지로 실행


![[Pasted image 20251231172257.png]]

![[Pasted image 20251231172307.png]]

![[Pasted image 20251231172321.png]]

![[Pasted image 20251231172239.png]]

![[Pasted image 20251231172350.png]]

![[Pasted image 20251231173901.png]]

![[Pasted image 20260102105435.png]]

![[Pasted image 20260102105446.png]]

![[Pasted image 20260102105505.png]]


# 배포까지에 순서 및 준비물

일단 순서가 로컬 -> 깃 액션 -> 도커허브 -> ec2 
이런식으로 진행된다고 하면 

기존에는 로컬에 있는 코드(소스들을) ec2에 올려서 실행했다고 하면
이제는 도커 이미지을 ec2환경에 올려서 실행할거임 이렇게 하면 일단 내가 생각 했을땐
편함 그리고 보안이 좋음 코드들을 그대로 올리는게 아니라서 그럼 


도커을 ec2환경에 올리려면 도커허브가 있어야함 로컬에 있는 도커 이미지을 클라우드에 올리다고 생각하면 됨 로그인하고 
![[Pasted image 20251231173901.png]]
password에 도커 허브 계정의 비밀번호 또는 토큰 입력
로컬에 이미지을 배포하기위해 로컬에서도 로그인 성공했으면
도커에 배포할 이미지을 생성해주고 
![[Pasted image 20260105112713.png]]
이 생성한 이미지에 

`형식: docker tag [로컬이미지] [계정명]/[레포이름]:[태그]`
docker tag mlservice yeotaeho/api-yeotaeho-corn:v1

1. 태그가 잘 생성되었는지 확인 (리스트에 두 이름이 다 보이면 성공)
docker images

2. 도커 허브로 전송 `docker push [계정명]/[레포이름]:[태그]`
docker push yeotaeho/test:v1

이런식으로 진행해주면

![[Pasted image 20260105112836.png]]
이렇게 나올거임 이제이걸을 ec2환경에 올려야 하는데 ec2환경에 올리고 실행할려면 ec2에 도커을 설치해줘야함 

ec2 환경 들어가서

curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh 
sudo usermod -aG docker $USER 
newgrp docker
docker --version

설치완료했으면 도커허브에 배포되어있는 이미지을 ec2로 풀 해볼거임
![[Pasted image 20260105113504.png]]
위 이미지에 오른쪽에 docker pull yeotaeho/apiserver:v1 이 명령어가 볼일거임 그대로 붙이면됨
![[Pasted image 20260105113618.png]]
그럼 docker images 쳤을때 이미지가 나오면 성공 
.env 없이는 컨테이너 실행이 안될거임

쨋든 풀 됐으면 된거임 이제 이런 과정이 깃 액션 하면 
자동으로 이미지가 빌드및 업데이트 되고 ec2환경에 빌드되는 것까지 해야함
그러면 먼저 깃 스크릿에 도커허브에 대한 토큰 유저 이름을 등록해줘야함 로그인을 위해 
![[Pasted image 20251231172350.png]]
등록 하는거는 도커허브 메모에 있으니 참고 

이렇게 세팅 후 제미나이 한테 물어봐 
깃허브액션의 자동 빌드 시스템 현재는 도커허브에 올린 이미지를 수동으로 ec2 에서 pull 해서 도커허브 에서 이미지를 가져와서 빌드했는데 , 이것을 깃허브액션에 자동으로 빌드시키고 싶어. 깃허브액션에 도커 아이디와 토큰은 저장한 후야. 이후 실행 과정을 알려줘.

이런식으로 커서한테도 전략 보여달라그러고

결국엔 이렇게 해서 깃 액션 했을때 이미지가 생성되고 도커허브에 자동으로 푸시 후 ec2 환경에 풀하고 실행까지 되면됨 

후에 nginx 을 ec2 환경에 설치
**nginx(리버스 프록시)**
프론트에서 https로 요청이 오면 그 요청이 합당한지 판단 검사하는 거임 정상적인 요청으로 판단되면 서버로 전달 그래서 설치하게 되면 https://12.23.45.124:8080/docs 로 스웨거가 나오게 됨
![[Pasted image 20260108175257.png]]

이역활을 프론트에선 버셀이 함
# vercel
![[Pasted image 20260105103148.png]]


![[Pasted image 20260105103533.png]]

# 리버스프록시 Reverse Proxy(Nginx)



![[Pasted image 20260105103155.png]]
![[Pasted image 20260105103535.png]]