# Nginx 설정 및 VOD 구성 가이드

## 🔧 Nginx 설정 반영 방법
1. 모든 설정 파일을 서버 경로 `/etc/nginx/sites-enabled` 에 복사
2. 설정 반영 명령어 실행:
   ```bash
   sudo nginx -t
   sudo systemctl reload nginx
   ```

---

## 📌 바꿔야 할 사항들
1. **Nginx 설정 파일**
   - `root` 경로 등 필요한 부분 수정

2. **config.json**
   - `players`의 각 `url` 수정
   - 필요 시 `experiments`의 `videoUrl`도 수정
   - ⚠️ 반드시 **nginx 설정 파일의 port와 config.json의 url port를 일치시켜야 함**

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

## 📂 VOD 권장 위치
- **360 비디오**
  - 압축 해제한 비디오를 `360eavp/content/myvideo/` (경로에 배치 여러 비디오 파일/디렉토리 포함 가능)

- **DASH VOD**
  - 압축 해제한 비디오를 **루트 디렉토리**에 배치
```
