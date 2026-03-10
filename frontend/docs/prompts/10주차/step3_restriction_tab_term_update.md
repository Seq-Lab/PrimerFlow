# Step3 제한효소 입력 이슈 수정 및 결과 새 탭 전환, template 용어 통일

## 1. 배경 및 목적

- Step 3의 `Restriction Enzymes` 입력 UI에서 값이 정상적으로 추가되지 않아, 설정값이 요청 payload에 반영되지 않는 문제를 해결.
- 결과를 모달에서만 확인하던 방식을 새 탭 페이지로 전환해 가독성과 브라우저 사용성을 개선.
- 도메인 용어를 `amplicon`에서 `template`으로 정리해 UI/타입/데이터 매핑의 의미를 일관화.

## 2. 프롬프트 (User Input)

```text
Step3 제한효소 설정 시 입력이 되지 않는 이슈를 수정해 주세요.
결과 표시를 모달이 아니라 새 탭(/result)에서 보이도록 바꿔 주세요.
기존 amplicon 용어는 template으로 통일해 주세요.

요구사항:
- 제한효소 입력은 Enter/Comma/Blur에서 토큰 추가되고, 중복은 막아 주세요.
- Generate 클릭 시 새 탭이 먼저 열리고, 분석 완료 후 resultKey를 붙여 결과 페이지로 이동하게 해 주세요.
- 팝업 차단/요청 실패 예외 처리도 포함해 주세요.
- 관련 타입/레이블/트랙 명칭에서 amplicon -> template 변경을 반영해 주세요.
```

## 3. AI 응답 요약 (AI Output)

- `Step3BindingLocation`에 제한효소 입력 토큰 로직을 적용:
  - `Enter`, `,`, `Blur` 시 입력값 정규화 후 추가.
  - 대소문자 기준 중복 방지.
  - 빈 입력 방지 및 `Backspace` 시 마지막 토큰 제거.
- `app/page.tsx`에서 `restrictionEnzymes` 상태를 관리하고, API 요청 payload의 `restriction_enzymes`에 전달.
- 결과 표시 플로우를 새 탭 기반으로 전환:
  - Generate 시 `window.open("/result", "_blank")`로 탭 선오픈.
  - 분석 완료 후 `resultKey`를 저장하고 `/result?resultKey=...`로 이동.
  - 팝업 차단 시 사용자 에러 메시지 노출, 실패 시 열린 결과 탭 정리.
- `app/result/ResultClientPage.tsx`에서 저장된 결과를 복원해 결과 화면을 렌더링.
- `template` 용어 중심으로 타입/매핑/라벨을 정리하고, 일부 로직은 `amplicon` 레이블도 함께 인식해 하위 호환성 유지.

## 4. 결과 및 적용 (Result)

- 적용 파일:
  - `components/steps/Step3BindingLocation.tsx`
  - `app/page.tsx`
  - `app/result/ResultClientPage.tsx`
  - `src/services/analysisService.ts`
  - `src/types/analysis.ts`
  - `src/lib/algorithms/focusViewState.ts`
- 주요 결과:
  - Step 3 제한효소 입력이 정상 동작하며, 설정값이 분석 요청에 반영됨.
  - 결과 확인 UX가 모달 의존에서 새 탭 기반으로 개선되어 분석/비교 작업이 수월해짐.
  - 프로젝트 전반 용어를 `template` 중심으로 통일해 문서/코드 의미 일관성을 확보.
