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




응용 프로그램: BrityRPA_Designer_Installer_v3.1.024.00624.exe
Framework 버전: v4.0.30319
설명: 응용 프로그램이System.Environment.FailFast(문자열 메시지)를 통해 프로세스 종료를 요청했습니다.
메시지: 
스택:
   위치: System.Environment.FailFast(System.String)
   위치: <Module>.‭⁬‎⁯⁫⁫‍⁮⁬​​‏‪‮‮‪⁫‪‭⁮⁫‫‮‎​‭‍​⁯​‮⁪‍⁯‮⁫⁯⁯⁫⁪‮()
   위치: <Module>..cctor()


오류 있는 응용 프로그램 이름: BrityRPA_Designer_Installer_v3.1.024.00624.exe, 버전: 3.1.24.624, 타임스탬프: 0x66791384
오류 있는 모듈 이름: unknown, 버전: 0.0.0.0, 타임스탬프: 0x00000000
예외 코드: 0x80131623
오류 오프셋: 0x00007ffd47facf2c
오류 있는 프로세스 ID: 0x1fc0
오류 있는 응용 프로그램 시작 시간: 0x01dbf9ff7bc66aa3
오류 있는 응용 프로그램 경로: C:\Users\~~\Downloads\BrityRPA_Designer (1)\BrityRPA_Designer_Installer_v3.1.024.00624.exe
오류 있는 모듈 경로: unknown
보고서 ID: 7bd6bc46-f31a-4c17-aa83-d5b00d30fc70
오류 있는 패키지 전체 이름: 
오류 있는 패키지에 상대적인 응용 프로그램 ID: 
