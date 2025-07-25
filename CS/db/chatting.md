# 채팅 기능 메시지 읽음 처리 및 안읽은 메시지 개수 표시 구현 방법

## 1. 데이터베이스 설계 접근법

## 방법 1: readCount 필드 사용

```sql
*-- 메시지 테이블*
CREATE TABLE messages (
    message_id BIGINT PRIMARY KEY,
    chat_room_id BIGINT,
    sender_id BIGINT,
    content TEXT,
    read_count INT DEFAULT 0, *-- 읽지 않은 사람 수*
    created_at TIMESTAMP
);
```

- **장점**: 조회 성능이 좋음
- **단점**: 메시지 수신 시마다 UPDATE가 발생하고, 데이터 정합성 문제가 발생할 수 있음

## 방법 2: lastReadMessage 필드 사용 (권장)

```sql
*-- 채팅방 참여자별 읽은 메시지 추적*
CREATE TABLE chat_participants (
    user_id BIGINT,
    chat_room_id BIGINT,
    last_read_message_id BIGINT,
    last_visit_time TIMESTAMP
);
```

- **장점**
    - 데이터 정규화로 불필요한 데이터 저장을 피함
    - 읽음 처리 시 단일 UPDATE만 필요
    - 데이터 정합성이 높음
    - 채팅방에 활발히 참여하는 사용자의 경우 불필요한 업데이트 최소화