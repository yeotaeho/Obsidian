
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