# Obsidian
개인 메모와 정리 하는 공간

- TIL
- Book
- CS


https://wikidocs.net/blog/@Allen/20309/


{
  "name": "주간 이슈 리포트 (매주 토요일)",
  "nodes": [
    {
      "parameters": {
        "rule": {
          "interval": [
            {
              "field": "weeks",
              "weeksInterval": 1,
              "triggerAtDay": [6],
              "triggerAtHour": 9
            }
          ]
        }
      },
      "id": "schedule-trigger",
      "name": "Schedule Trigger",
      "type": "n8n-nodes-base.scheduleTrigger",
      "typeVersion": 1.2,
      "position": [-1120, 0]
    },
    {
      "parameters": {
        "jsSode_note": "n8n Code node - 최근 7일 시작 날짜(ISO) 계산",
        "jsCode": "const since = $now.minus({ days: 7 }).toISO();\nreturn [{ json: { since } }];"
      },
      "id": "code-since-date",
      "name": "Since 날짜 계산",
      "type": "n8n-nodes-base.code",