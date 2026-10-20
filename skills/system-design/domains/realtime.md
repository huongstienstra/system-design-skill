# Realtime: chat, live feeds, presence, notifications

## Questions
Ranked; re-rank for the prompt. The default follows each one.

1. Can a message ever be lost? Default: never; at-least-once delivery plus dedupe by message ID.
2. Must messages arrive in order? Default: in order within a conversation.
3. Group size? Default: up to 100.
4. Users online at peak? Default: 10% of daily active users.
5. How long is history kept? Default: forever, searchable for 1 year.
6. Read receipts, presence, or typing indicators? Default: read receipts only.
7. Urgent alerts or escalation? Default: push notification when the user is offline.
8. Moderation or compliance (reporting, retention, legal hold)? Default: user reporting plus admin delete.

## Scale dimensions
- Concurrent connections: users online at peak; each holds one socket.
- Messages/sec: active users × messages per user per minute ÷ 60.
- Fan-out: messages/sec × average group size = deliveries/sec.
- Presence traffic: online users × heartbeat rate; often larger than the messages.
- Storage: messages per day × message size × retention.

## Usual failures
- A connection drops; messages sent while the client was away must be replayed.
- A server holding sockets restarts and thousands of clients reconnect at once.
- A retry delivers a message twice, or two servers assign conflicting order.
- A large group turns one message into a fan-out storm.
- A slow consumer lets its outbound buffer grow until the server runs out of memory.
- Presence shows users online after they left.
- Push notifications arrive late or duplicate the in-app message.
- Abuse: spam, harassment, or a flood from one account.
