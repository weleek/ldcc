### Windows 개발환경 설정

- WSL2 에서 SSH 서비스 구동

```bash
#패키지 업데이트하기
sudo apt update
#SSH서버 설치하기
sudo apt install openssh-server
#ssh셋팅하기
sudo systemctl enable ssh
#ip주소 확인하기
sudp apt install net-tools
ifconfig
#호스트키 생성
sudo ssh-keygen-A
#비밀번호 인증방식 사용
sudo nano /etc/ssh/sshd_config
#다음 내용을 수정합니다.
PasswordAuthentication no >> yes
#ssh 서비스 시작
sudo service ssh start
```
