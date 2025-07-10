# Obsidian
개인 메모와 정리 하는 공간

- TIL
- Book
- CS


# 로그 파일 경로 설정
$logPath = "D:\Scripts\restart_log.txt"
$timeStamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"

# 작업 스케줄러 실행 로그 기록
"$timeStamp - 작업 스케줄러에 의해 스크립트 실행됨." | Out-File -Append -Encoding utf8 $logPath

# 실행할 Brity RPA BOT 경로 설정
$BrityPath = Join-Path $env:APPDATA "Brity RPA BOT\BrityRPA_BOT.exe"

# Brity RPA BOT 실행 여부 확인
$process = Get-Process | Where-Object { $_.ProcessName -like "*Brity*" }

if ($process) {
    # 실행 중인 프로세스의 실행 파일 경로 가져오기
    $runningPaths = $process | ForEach-Object { $_.Path }  

    if ($runningPaths -contains $BrityPath) {
        "$timeStamp - Brity RPA BOT이 이미 실행 중입니다." | Out-File -Append -Encoding utf8 $logPath
        exit  # 실행 중이면 스크립트 종료
    }
}

# Brity RPA BOT 실행 여부 확인 후 실행
if (Test-Path $BrityPath) {
    Start-Process $BrityPath
    "$timeStamp - Brity RPA BOT을 실행했습니다: $BrityPath" | Out-File -Append -Encoding utf8 $logPath
} else {
    "$timeStamp - Brity RPA BOT 실행 파일을 찾을 수 없습니다: $BrityPath" | Out-File -Append -Encoding utf8 $logPath
}
