# Nginx 설정 및 VOD 구성 가이드

## 🔧 Nginx 설정 반영 방법
1. 모든 설정 파일을 서버 경로 `/etc/nginx/sites-enabled` 에 복사
2. 설정 반영 명령어 실행:
   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```

---

## 바꿔야 할 사항들
1. **Nginx 설정 파일**
   - `root` 경로 등 필요한 부분 수정

2. **config.json**
   - `players`의 각 `url` 수정
   - 필요 시 `experiments`의 `videoUrl`도 수정
   - 반드시 **nginx 설정 파일의 port와 config.json의 url port를 일치시켜야 함**

3. **360eavp의 default.json**
   - `baseUrl`을 nginx 서버에 맞게 수정
   - 예시:
     ```json
     "baseUrl": "https://10.20.13.157:8080/content/myvideo/"
     ```
   - IP와 Port는 **360eavp player의 nginx 설정과 동일**해야 함

4. **dash.js 빌드**
   - 루트에서 다음 명령어 실행:
     ```bash
     npm install --ignore-scripts
     npm run webpack-build-modern && npm run webpack-build-legacy
     ```

---

## VOD 권장 위치
- **360 비디오**
  - 압축 해제한 비디오를 `360eavp/content/myvideo/` 경로에 배치 (여러 비디오 파일/디렉토리 포함 가능)

- **DASH VOD**
  - 압축 해제한 비디오를 **루트 디렉토리**에 배치


## HLS LIVE
1. docker로 OvenMediaEngine 실행(예시): 
    ```bash
    sudo docker run --name ome -d -e OME_HOST_IP=10.20.13.157 -p 1935:1935 -p 9999:9999/udp -p 9000:9000 -p 3333:3333 -p 3334:3334 -p 3478:3478 -p 10000-10009:10000-10009/udp -v "$HOME/yeon/metric/conf/Server.xml":/opt/ovenmediaengine/bin/origin_conf/Server.xml -v "$HOME/yeon/tls/cert.crt":/opt/ovenmediaengine/bin/origin_conf/cert.crt:ro -v "$HOME/yeon/tls/cert.key":/opt/ovenmediaengine/bin/origin_conf/cert.key:ro -v "$HOME/yeon/tls/cert.ca-bundle":/opt/ovenmediaengine/bin/origin_conf/cert.ca-bundle:ro airensoft/ovenmediaengine:v0.20.0
    ```

    위 예시에서 OME_HOST_IP는 변경, -v로 제공되는 마운트 옵션은 아래 참고
    - 마운트 옵션 (-v)
        - 1번: 루트의 conf의 Server.xml을 수정하여 자유롭게 트랜스코딩 설정을 변경 가능
        - 2~4번: https로 서빙하기 위한 각종 인증서 파일을 서버에 준비하고, 경로에 맞게 왼쪽 부분을 수정
            - 예시: "$HOME/yeon/tls/cert.ca-bundle"

2. OBS 설치 및 실행
    - 상단 메뉴의 파일 > 설정 > 방송에서 서버 란에 url 입력 및 스트림 키 입력
        - 서버: rtmp://{docker server ip}:1935/app
        - 스트림 키: stream
        
    - 추가로, 파일 > 설정 > 출력에서 설정 변경 (OME 권장 설정)
        - 키프레임 간격: 1 (0으로 설정 X)
        - CPU 사용량 사전 설정: ultrafast
        - 프로파일: baseline
        - 조정: zerolatency
        
위 과정까지 하면, obs를 통한 송출 및 이를 트랜스코딩하는 비디오 서버 준비 완료
hls 플레이어(ovenplayer) 접속하여, url(예시: https://10.20.13.197:3334/app/stream/llhls.m3u8)을 입력하여 재생 (혹은 자동 재생 기능 사용)
