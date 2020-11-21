# let us: go! 2020 Fall

## GoogleSheet로 .strings 파일 관리하기 (문구 수정 떠넘기기)

목표 : GoogleSheet에 있는 값을 바꾸면 자동으로 .strings 파일에 동기화되어 반영되는 것   

1. Google Sheet를 보기 좋게 만든다.
- 문구 수정이 이루어지는 공간.
- screen(화면), key, kr, en, … 컬럼을 생성한다.
2. Localization 설정
- .strings 파일 생성 Xcode에서 한번만 작업해주면 됨.
- 빌드할 때마다 문자열이 동기화되는 파일
- 다국어 지원의 경우 여러 개의 .strings 파일을 만들어 진행
3. Script Flow 그리기
- GoogleSheets csv 추출
- URL에 DOC-ID를 넣어 검색창에 입력하면 csv가 다운됨. https://docs.google.com/spreadsheets/d/{DOC-ID}/export?format=csv
- 세마포어, URLSessionDataDelegate 사용
- csv를 .strings 형태로 변환
- key, value 형태인 Dictionary로 변환.
- .string 파일을 프로젝트에 저장
- macOS에서 실행되는 스크립트이기 때문에 상대 경로로 지정.
4. Script 설정
- 프로젝트에 New Run Script Phase 추가하여 스트립트 경로 입력

Q. 매 번 스크립트 빌드하는 이유
A. 이유가 있진 않았고, Sheet를 한 번 더 거치는 방법을 적용할 수 있을 것임.
