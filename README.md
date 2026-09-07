# Obsidian
개인 메모와 정리 하는 공간

- TIL
- Book
- CS


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
      "typeVersion": 2,
      "position": [-900, 0]
    },
    {
      "parameters": {
        "resource": "issue",
        "operation": "getAll",
        "owner": {
          "__rl": true,
          "value": "REPO_OWNER_여기에_입력",
          "mode": "list"
        },
        "repository": {
          "__rl": true,
          "value": "REPO_NAME_여기에_입력",
          "mode": "list"
        },
        "returnAll": true,
        "filters": {
          "since": "={{ $json.since }}",
          "state": "all"
        }
      },
      "id": "github-get-issues",
      "name": "GitHub - Get All Issues",
      "type": "n8n-nodes-base.github",
      "typeVersion": 1,
      "position": [-680, 0],
      "credentials": {
        "githubApi": {
          "id": "REPLACE_WITH_CREDENTIAL_ID",
          "name": "GHE Account (githubsamsungds.net)"
        }
      }
    },
    {
      "parameters": {
        "jsCode": "// 이슈들을 생성일(created_at) 기준으로 날짜별 그룹핑 후 마크다운 조립\nconst items = $input.all();\n\nconst groups = {};\nfor (const item of items) {\n  const issue = item.json;\n  const date = issue.created_at ? issue.created_at.slice(0, 10) : 'unknown';\n  if (!groups[date]) groups[date] = [];\n  groups[date].push(issue);\n}\n\nconst sortedDates = Object.keys(groups).sort();\n\nlet md = `# 주간 이슈 리포트\\n\\n`;\nfor (const date of sortedDates) {\n  const d = new Date(date);\n  const weekday = ['일','월','화','수','목','금','토'][d.getDay()];\n  md += `## ${date} (${weekday})\\n\\n`;\n  for (const issue of groups[date]) {\n    const author = issue.user ? issue.user.login : 'unknown';\n    md += `- #${issue.number} ${issue.title} (작성자: @${author})\\n`;\n  }\n  md += `\\n`;\n}\n\nif (sortedDates.length === 0) {\n  md += `이번 주에는 등록된 이슈가 없습니다.\\n`;\n}\n\nreturn [{ json: { markdown: md, issueCount: items.length } }];"
      },
      "id": "code-format-report",
      "name": "날짜별 그룹핑 & 마크다운 포맷",
      "type": "n8n-nodes-base.code",
      "typeVersion": 2,
      "position": [-460, 0]
    },
    {
      "parameters": {
        "resource": "issue",
        "operation": "create",
        "owner": {
          "__rl": true,
          "value": "REPO_OWNER_여기에_입력",
          "mode": "list"
        },
        "repository": {
          "__rl": true,
          "value": "REPO_NAME_여기에_입력",
          "mode": "list"
        },
        "title": "=주간 이슈 리포트 - {{ $now.toFormat('yyyy-MM-dd') }}",
        "body": "={{ $json.markdown }}",
        "labels": ["weekly-report"]
      },
      "id": "github-create-issue",
      "name": "GitHub - Create Issue",
      "type": "n8n-nodes-base.github",
      "typeVersion": 1,
      "position": [-240, 0],
      "credentials": {
        "githubApi": {
          "id": "REPLACE_WITH_CREDENTIAL_ID",
          "name": "GHE Account (githubsamsungds.net)"
        }
      }
    }
  ],
  "connections": {
    "Schedule Trigger": {
      "main": [
        [
          {
            "node": "Since 날짜 계산",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "Since 날짜 계산": {
      "main": [
        [
          {
            "node": "GitHub - Get All Issues",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "GitHub - Get All Issues": {
      "main": [
        [
          {
            "node": "날짜별 그룹핑 & 마크다운 포맷",
            "type": "main",
            "index": 0
          }
        ]
      ]
    },
    "날짜별 그룹핑 & 마크다운 포맷": {
      "main": [
        [
          {
            "node": "GitHub - Create Issue",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "active": false,
  "settings": {
    "executionOrder": "v1"
  }
}



{
  "name": "조직 레포 이슈 조회",
  "nodes": [
    {
      "parameters": {},
      "id": "manual-trigger-1",
      "name": "수동 실행",
      "type": "n8n-nodes-base.manualTrigger",
      "typeVersion": 1,
      "position": [260, 300]
    },
    {
      "parameters": {
        "authentication": "accessToken",
        "resource": "issue",
        "operation": "getAll",
        "owner": {
          "__rl": true,
          "value": "YOUR_ORG_NAME",
          "mode": "list"
        },
        "repository": {
          "__rl": true,
          "value": "YOUR_REPO_NAME",
          "mode": "list"
        },
        "returnAll": true,
        "filters": {
          "state": "all"
        }
      },
      "id": "github-node-1",
      "name": "이슈 조회",
      "type": "n8n-nodes-base.github",
      "typeVersion": 1,
      "position": [480, 300],
      "credentials": {
        "githubApi": {
          "id": "REPLACE_WITH_CREDENTIAL_ID",
          "name": "GitHub Enterprise (githubsamsungds.net)"
        }
      }
    }
  ],
  "connections": {
    "수동 실행": {
      "main": [
        [
          {
            "node": "이슈 조회",
            "type": "main",
            "index": 0
          }
        ]
      ]
    }
  },
  "pinData": {},
  "meta": {
    "instanceId": "template"
  }
}
