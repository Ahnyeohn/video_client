nginx setting

nginx 내의 설정 파일들을 전부 서버 경로의 /etc/nginx/sites-enabled 에다가 복사하고,
이 설정파일을 반영하려면,
sudo nginx -t
sudo systemctl reload nginx 
위 명령어 사용


바꿔야 할 사항들:
1) nginx 설정 파일 내용들 (root 경로 등...)
2) config.json에 있는 players의 각 url
   만약 자동실험까지 필요하다면, experiments에 있는 각 videoUrl도 수정
   지금 보면, nginx 설정 파일에 있는 port가 config.json에 있는 각 url의 port와 일치하지 않을 것이다.
   반드시 일치시켜야 함
4) 360eavp의 default.json 파일의 baseUrl을 nginx 서버에 맞게 수정
   예시: "https://10.20.13.157:8080/content/myvideo/" 이렇게 되어 있으면 ip랑 port를 맞게 수정해야 한다
   이때, port는 360eavp player의 nginx에서 설정한 ip 및 port와 반드시 같아야 한다.