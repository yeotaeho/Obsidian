# AWS 배포기 — EC2 · GitHub Actions CI/CD · S3 · Nginx

로컬에서 만든 서비스를 AWS에 올리는 전체 과정을 정리했습니다. EC2 인스턴스 생성부터 GitHub Actions CI/CD, S3, 리버스 프록시(Nginx)까지 이어집니다.

## 0. AWS 기본 용어

- **루트 사용자 / IAM 사용자**: 계정 권한 체계
- **RDS** → DB
- **EC2** → 서버
- **S3** → 디스크 (버킷 = 컨테이너)

> **아마존과 연동하는 과정의 핵심은 `로컬(내 PC) → Git → AWS`로 이어지는 흐름이며, 여기서 Git이 중재자 역할을 합니다.**

내 PC에서 Git으로 갈 때는 그냥 `push`하면 되지만, **Git에서 AWS로 갈 때는 GitHub Actions를 따로 설정**해 줘야 합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219131506.png]]
자신의 프로젝트를 Git을 통해 아마존에 연결하도록 명령을 주는 구조입니다.

## 1. EC2 인스턴스 생성

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218154906.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218154952.png]]
**Instances**를 클릭합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218155313.png]]
인스턴스 이름을 작성합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218155500.png]]
어떤 OS/사양을 선택할지는 프로젝트 기술 스택에 따라 다르므로, 자신의 스택에 맞춰 결정합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218155519.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218160432.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218160453.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218160500.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218160508.png]]
**Create new key pair**를 클릭해 키 페어를 생성합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218160558.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218160741.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218163109.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218163220.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218163352.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218163604.png]]
<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218164015.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218164302.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218164057.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218164413.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218164802.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218164750.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218165205.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218165533.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218165909.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218165943.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218170242.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218170413.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218171736.png]]

## 2. GitHub Actions로 CI/CD 연동

FastAPI를 연결된 EC2에 GitHub Actions(CI/CD)로 배포하기 위해, 먼저 시크릿을 설정합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218175455.png]]
올린 프로젝트의 **Settings** 탭으로 이동합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218175258.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218175305.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218175639.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218180049.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218180126.png]]
이 값들은 실제 작업을 진행한 도구(커서 등)에 물어보며 채워 넣습니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218181204.png]]
<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218181213.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219102419.png]]
참고로 **4번의 값이 호스트(`EC2_HOST`)** 입니다.

## 3. EC2(우분투) 환경 준비

아마존(우분투)에 올리려면, **올릴 폴더와 동일한 구조로 우분투에도 디렉터리를 맞춰** 줘야 합니다. 예를 들어 백 서버(api)를 올린다면 백 서버의 폴더·파일 구조를 그대로 맞춥니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219111522.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219111938.png]]
기본 명령은 다음과 같습니다.

```bash
mkdir 이름   # 폴더 생성
touch 이름   # 파일 생성
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219113524.png]]
파일 편집은 `vim`을 사용합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219113750.png]]
들어가면 이런 화면이 나오고,

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219113710.png]]
이것이 나가는(저장/종료) 명령입니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219112903.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251218182129.png]]

## 4. 코드 업로드 — SCP / rsync / git clone

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219154117.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219154126.png]]

api 폴더 전체 업로드(`model_weights` 제외):

```bash
scp -i "Dovahkiin.pem" -r api/* ubuntu@ec2-3-39-230-121.ap-northeast-2.compute.amazonaws.com:~/api/
```

또는 rsync 사용(더 효율적, 설치되어 있다면):

```bash
rsync -avz --exclude 'model_weights' --exclude '__pycache__' --exclude '*.pyc' --exclude 'venv' \
  -e "ssh -i Dovahkiin.pem" api/ ubuntu@ec2-3-39-230-121.ap-northeast-2.compute.amazonaws.com:~/api/
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219154133.png]]
api 폴더만 클론하기는 어려우므로, 전체 레포를 클론한 뒤 api만 사용합니다.

```bash
cd ~
git clone https://github.com/yeotaeho/learn_4.git temp_repo
cp -r temp_repo/api ~/api
rm -rf temp_repo
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219184142.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219184155.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251219190026.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251222110948.png]]
OS 이미지(예: 윈도우)와 머신 이미지 구분도 함께 확인합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251222114327.png]]

## 5. S3 버킷과 CORS / IAM 정책

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229160240.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229160252.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229165205.png]]
세팅 항목들은 용도에 맞게 설정합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229165258.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229165354.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229165359.png]]

CORS 설정은 자신의 도메인에 맞춰 작성합니다.

```json
[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["GET", "PUT", "POST", "DELETE"],
        "AllowedOrigins": ["http://yeotaeho.kr"],
        "ExposeHeaders": ["ETag"],
        "MaxAgeSeconds": 3600
    }
]
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229165438.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229165447.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229170553.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229170800.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229170917.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229170953.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229171107.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229171403.png]]

IAM 정책 예시:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:PutObject", "s3:DeleteObject"],
      "Resource": "arn:aws:s3:::yeotaeho/*"
    }
  ]
}
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229173428.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229173819.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251229174148.png]]

## 6. 배포 전략 — 소스 배포 vs 도커 이미지 배포

두 가지 방식이 있습니다.

- **기존: `git action → AWS`** — 소스를 올려서 실행
- **개선: `git action → Docker Hub → AWS`** — 도커 이미지로 실행

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251231172257.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251231172307.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251231172321.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251231172239.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251231172350.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251231173901.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260102105435.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260102105446.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260102105505.png]]

## 7. 배포 순서와 도커 이미지로 올리기

전체 순서는 `로컬 → 깃 액션 → 도커 허브 → EC2`입니다.

기존에는 로컬의 소스 코드를 EC2에 올려 실행했다면, 이제는 **도커 이미지를 EC2 환경에 올려 실행**합니다.

> **도커 이미지로 배포하면 편하고 보안에도 유리합니다. 코드를 그대로 올리는 게 아니기 때문입니다.**

도커를 EC2에 올리려면 Docker Hub가 필요합니다(로컬 이미지를 클라우드에 올린다고 생각하면 됩니다). 로그인부터 진행합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251231173901.png]]
Password에는 Docker Hub 계정 비밀번호 또는 토큰을 입력합니다. 로컬에서 로그인에 성공했으면 배포할 이미지를 생성합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260105112713.png]]

```bash
# 형식: docker tag [로컬이미지] [계정명]/[레포이름]:[태그]
docker tag mlservice yeotaeho/api-yeotaeho-corn:v1

# 1. 태그가 잘 생성됐는지 확인 (리스트에 두 이름이 다 보이면 성공)
docker images

# 2. 도커 허브로 전송: docker push [계정명]/[레포이름]:[태그]
docker push yeotaeho/test:v1
```

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260105112836.png]]
이제 이 이미지를 EC2에 올려 실행해야 하는데, 그러려면 EC2에 도커를 설치해야 합니다.

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker $USER
newgrp docker
docker --version
```

설치를 마쳤으면, Docker Hub에 배포된 이미지를 EC2로 pull합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260105113504.png]]
위 이미지 오른쪽에 보이는 `docker pull yeotaeho/apiserver:v1` 명령을 그대로 붙여 넣으면 됩니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260105113618.png]]
`docker images`를 쳤을 때 이미지가 보이면 성공입니다. 단, **`.env`가 없으면 컨테이너 실행이 안 됩니다.**

## 8. 자동화 — 깃 액션으로 빌드·푸시·풀

지금까지는 수동이었지만, 목표는 **깃 액션 시 자동으로 이미지가 빌드·업데이트되고, EC2 환경까지 자동 빌드**되는 것입니다. 그러려면 먼저 깃 시크릿에 Docker Hub 토큰과 유저 이름을 등록해 로그인할 수 있게 해야 합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20251231172350.png]]
등록 방법은 Docker Hub 메모를 참고합니다.

> 세팅 후 AI 도구에게 다음과 같이 물어보면 워크플로를 받을 수 있습니다.
> "현재는 도커허브에 올린 이미지를 수동으로 EC2에서 pull해 빌드했는데, 이것을 GitHub Actions에서 자동으로 빌드시키고 싶다. 도커 아이디와 토큰은 GitHub Actions에 저장한 상태다. 이후 실행 과정을 알려달라."

최종적으로는, 깃 액션 시 이미지가 생성되어 Docker Hub에 자동 푸시되고, EC2에서 pull·실행까지 되면 완성입니다.

## 9. 리버스 프록시 — Nginx

배포 이후 EC2에 **Nginx(리버스 프록시)** 를 설치합니다.

> **Nginx(리버스 프록시): 프론트에서 HTTPS 요청이 오면 그 요청이 합당한지 검사하고, 정상으로 판단되면 서버로 전달합니다.**

설치하면 `https://12.23.45.124:8080/docs`로 스웨거가 노출됩니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260108175257.png]]

이 역할을 프론트엔드에서는 **Vercel**이 수행합니다.

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260105103148.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260105103533.png]]

<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260105103155.png]]
<!-- TODO: 이미지 업로드 필요 -->
![[Pasted image 20260105103535.png]]
