# 통합 검증 결과

2026-09-08, 실제 Spring Boot·JWT·HSQL 메모리 DB·파일 저장소와 Chrome을 사용했다. API 응답 모의나 제품 코드 대체는 하지 않았다.

## 검증 대상

백엔드 기준은 eGovFramework/egovframe-template-simple-backend의 main d63e3e4다. 아래 네 변경을 함께 반영한 별도 작업 트리에서 통합 검증했다.

- 이미지: b1f1a441dd5da9bc2c75d90d8fd4d0ecb4c37497
- 일정: 7e54549dace30292f9817b3a7cc8e4227e45c073
- 작성자 검색: f558e36788fbb017394688350c31babbfef3e853
- 다중 첨부: 0924b20262652d5bb6bd8345f12771f3bad5b1c5

React는 eGovFramework/egovframe-template-simple-react의 main 88647b1에 일정 변경 4f34fb7cc54b2ddf11cea57cb52a15457007ad16을 반영했다. 각 변경은 별도 PR로 제출한다.

## 결과

| 항목 | 결과 |
| --- | --- |
| Maven 비브라우저 전체 테스트 및 패키징 | 209건 통과, 실패/오류/건너뜀 0 |
| 이미지 HTTP·파일 검증 | 5건 통과 |
| 다중 첨부 HTTP·DB·파일 검증 | 8건 통과 |
| 일정 HTTP·DB 검증 | 12건 통과 |
| 작성자 검색 HSQL·서비스·DAO 검증 | 19건 통과 |
| React 자동 코드 테스트 | 관련 Vitest 26건, ESLint·배포용 빌드 통과 |
| Chrome 실제 연동 | 일정 정상 상세/수정·첨부 다운로드, 부재 시 네 화면 목록 복귀, 갤러리 정상/실파일 유실 확인 |

Maven 명령: `mvn -B package --file pom.xml -DexcludedGroups=browser`.

- [이미지·업로드 실제 응답 및 저장 상태](results/file-http.json)
- [일정 실제 응답 및 DB 검증](results/schedule-http.json)
- [작성자 검색 실제 쿼리 결과](results/author-search.txt)

업로드 거부 5개 조합에서는 새 물리 파일·게시글·첨부 행이 남지 않았고 기존 파일의 SHA-256도 유지됐다. 기존 거부 응답 HTTP 500은 변경하지 않았다. 정상 순서, 정확히 5MiB, 이름 있는 0바이트 업로드를 확인했다. 빈 파일 다운로드 개선은 이 검증 범위에 포함하지 않았다.

작성자 검색은 현재 회원명 우선, 실제 회원 삭제 후 저장 이름 검색, 목록/개수·제목/내용·페이징을 확인했다. 실제 DB 실행은 HSQL이며 다른 5종 DB는 자동 SQL 생성·바인딩 검증까지 수행했다.

일정 부재는 HTTP 200 / 본문 resultCode 404 / 빈 result다. 배포용 React의 관리자 상세·수정, 오늘·금주 행사 상세에서 안내 한 번 후 원래 목록으로 복귀했다. 관리자 월·일정구분도 유지됐다. 정상 첨부 다운로드는 원본 68바이트와 같았다.

## 스크린샷 범위

screenshots의 이미지는 수정 후 코드에서 촬영한 실제 화면이다. 코드 수정 전 캡처로 표기하지 않는다.

- gallery-image-normal.png / gallery-image-missing.png: 같은 갤러리 게시글의 정상 이미지와 물리 파일 유실 상태. 정상 40,026바이트 PNG·원본 일치, 유실 후 HTTP 404·0바이트를 따로 확인했다.
- normal-schedule-detail.png: 정상 일정 상세와 첨부.
- schedule-admin-return.png: 삭제된 일정 수정 화면에서 안내를 확인한 뒤 2026년 9월·회의 조건을 유지하며 돌아온 목록.
- schedule-daily-return.png / schedule-weekly-return.png: 삭제된 상세에서 안내 후 돌아온 오늘/금주 행사 목록.

목록 화면 캡처 자체가 안내창이나 HTTP 응답을 나타내는 것은 아니다. 실제 대화상자와 네트워크 응답은 별도로 검증했다. 테스트 계정과 게시글·첨부는 공개 샘플 및 검증용 데이터다. 테스트 서버와 브라우저는 종료했다.
