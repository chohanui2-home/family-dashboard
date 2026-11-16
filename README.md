# 우리 가족 기록장 (Family Dashboard)

가족이 스마트폰 브라우저에서 함께 쓰는 실시간 기록장입니다.
할 일 · 공부 · 용돈 · 도장판 · 가족 채팅을 한 화면에서 보고, 바꾼 내용은 가족 모두의 화면에 몇 초 안에 반영됩니다.

## 어떻게 동작하나요

- 이 저장소에는 **화면(HTML/CSS/JS)만** 들어 있습니다. 가족 이름·할 일·채팅 같은 기록은 하나도 들어 있지 않습니다.
- 기록은 Supabase(무료 Postgres)의 `family_rooms` 테이블에 저장됩니다.
- 이 페이지를 그냥 열면 **잠금 화면**만 보입니다. `#k=...` 접속 코드와 4자리 가족 PIN을 알아야 기록을 읽고 쓸 수 있습니다.
- 테이블은 RLS(Row Level Security)가 켜져 있고 정책이 없어서, `anon` 키로 테이블을 직접 읽거나 쓰는 것은 막혀 있습니다. 오직 아래 RPC 함수만 열려 있습니다.
  - `family_load(p_key, p_pin)` — 접속 코드 + PIN이 맞을 때만 기록과 `updated_at` 반환
    - `family_save(p_key, p_pin, p_state)` — 같은 조건에서만 저장
      - `family_change_pin(p_key, p_old_pin, p_new_pin)` — PIN 변경
      - 실시간 반영은 Supabase Realtime Broadcast(변경 알림) + 2.5초 폴링(fallback)으로 이루어집니다.

      ## 사용법

      1. 가족에게 받은 링크(`...#k=접속코드`)를 엽니다.
      2. 4자리 가족 PIN을 입력합니다.
      3. 처음 한 번만 "내 기기 역할"(가족 구성원)을 고릅니다. 이후에는 이 기기에서 바로 열립니다.
      4. 설정(⚙️)에서 가족 PIN 바꾸기, 초대 링크 복사, 이 기기 잠그기를 할 수 있습니다.

      ## 배포

      GitHub Pages — `main` 브랜치 루트의 `index.html`.
      
