# Obsidian
개인 메모와 정리 하는 공간

- TIL
- Book
- CS


{
  "name": "조직 레포 목록 조회 (HTTP Request)",
  "nodes": [
    {
      "parameters": {},
      "id": "manual-trigger",
      "name": "When clicking Execute",
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [-900, 0]
    },
    {
      "parameters": {
        "jsCode": "return [{ json: { org: 'ORG_이름_여기에_입력' } }];"
      },
      "id": "set-org",
      "name": "조직명 설정",
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [-680, 0]
    },
    {
      "parameters": {
        "method": "GET",
        "url": "=https://githubsamsungds.net/api/v3/orgs/{{ $json.org }}/repos",
        "authentication": "genericCredentialType",
        "genericAuthType": "httpHeaderAuth",
        "sendQuery": true,
        "queryParameters": {
          "parameters": [
            { "name": "type", "value": "all" },
            { "name": "per_page", "value": "100" },
            { "name": "page", "value": "1" }
          ]
        },
        "sendHeaders": true,
        "headerParameters": {
          "parameters": [
            { "name": "Accept", "value": "application/vnd.github+json" },
            { "name": "X-GitHub-Api-Version", "value": "2022-11-28" }
          ]
        },
        "options": {}
      },
      "id": "http-get-org-repos",
      "name": "HTTP Request - Get Org Repos",
      "type": "n8n-nodes-base.httpRequest",
      "typeVersion": 4.2,
      "position": [-460, 0],
      "credentials": {
        "httpHeaderAuth": {
          "id": "REPLACE_WITH_CREDENTIAL_ID",
          "name": "GHE Header Auth (Authorization: token ...)"
        }
      }
    }
  ],
  "connections": {
    "When clicking Execute": {
      "main": [
        [
          { "node": "조직명 설정", "type": "main", "index": 0 }
        ]
      ]
    },
    "조직명 설정": {
      "main": [
        [
          { "node": "HTTP Request - Get Org Repos", "type": "main", "index": 0 }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  }