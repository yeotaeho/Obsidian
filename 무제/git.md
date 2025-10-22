

git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/yeotaeho/-.git
git push -u origin main

원래 있던 레파지토리에 넣을 경우
git add .
git commit -m "아무거나 적어도 상관없음"
git push 

깃허브에 올려도 안올라가는경우

![[Pasted image 20251015160106.png]]

커밋만 있고 푸시가 재대로 안된겨우

git status 라고 쳤는데

`Changes not staged for commit` 또는 `Untracked files`가 나온다면 파일이 재대로 커밋되지 않음

`Your branch is ahead of 'origin/main' by X commits`라면 커밋은 되었지만 아직 푸시가 안 됌



`Changes not staged for commit` 또는 `Untracked files`인 경우 git add .실행 현재폴더에 모든변경파일 추가 후
git commit -m "Add project files"파일을 커밋한 후 git push origin main 푸시 여기서 main은 브랜치 이름으로 잘 맞춰서 넣으


`Your branch is ahead of 'origin/main' by X commits`이렇게 뜨면 폴더경로 설정후 cmd창으로 
예: cd C:\Users\hi\Documents\project\Spring

