Link - http://pheadra.tistory.com/entry/Android-adb-shell%EC%97%90%EC%84%9C-%EC%9C%A0%EC%9A%A9%ED%95%9C-%EB%AA%85%EB%A0%B9%EC%96%B4

```
시스템 기본정보: 하드웨어, 커널 등
cat /proc/version : 커널 버전
cat /proc/cpuinfo : 프로세서 정보, CPU타입, 모델 제조사 등
cat /porc/meminfo : 메모리 정보, 실제 메모리 및 가상 메모리
cat /proc/devices : 현재 커널에 설정되어 있는 장치 목록
mount : 마운트된 모든 장치 정보
df : 하드디스크 사용량
cat /porc/filesystems : 커널에 설정되어 있는 파일시스템 목록
cat /proc/swaps : 스왑 파티션의 크기와 사용량
cat /proc/interrupts : 장치가 사용중인 인터럽트(IRQ)목록 표시
cat /proc/ioports : 현재 사용중인 input/output 포트
cat /proc/partitions : 파티션 정보
cat /proc/uptime : 시스템이 얼마나 살아있었는지
cat /proc/stat : 시스템 상태에 관한 다양한 정보, CPU 사용 통계, 부팅이후 page fault 발생 횟수 등
cat /proc/zoneinfo : ZONEINFO
dmesg : 시스템 부팅시 나왔던 메시지
ps : 실행중인 프로세스 정보
ps -p - t : 프로세스와 쓰레드 목록
set or printenv : 환경설정값 출력


시스템 리소스 사용 현황
vmstat : 시스템 리소스 상황 모니터, CPU, I/O, Memory 등
cat /proc/diskstats : 디스크 utilization과 throughput. 즉 디스크 I/O현황
top : 시스템 프로세스 상황 모니터링/ 프로세스별 CPU사용량, 메모리와 스왑 사용량 등
procrank : 프로세스별 메모리
dumpsys meminfo [PID] : 해당 프로세스 메모리 상세 정보
cat /proc/[PID]/stat : 해당 프로세스에 대한 정보, 시작시간, 상태, CPU 사용량 등
cat /proc/[PID]/maps : 해당 프로세스의 메모리 맵 정보
cat /proc/vmstat : 버추얼 메모리 통계?
librank : 라이브러리별 메모리 사용량?


네트워크 관련
cat /proc/net/netlink : 네트워크 정보
netcfg : 네트워크 인터페이스와 IP주소 목록
netstat : 네트워크 연결상태 확인
nc : 네트워크용 cat 명령어(netcat)
ifconfig : 네트워크 인터페이스 설정 정보. 장치명을 파라미터로 받음. ip 주소. 서브넷마스크 등
tcpdump : 실시간 패킷 모니터링
iftop : 네트워크를 위한 top
route : 해당 호스트까지 연결하는 중간 경로 정보인 라우팅 테이블 표시
ping : 원격 호스트와의 연결 테스트
cat /proc/net/route : Route


안드로이드 제공
logcat : 로그캣 보기
pm : package manager의 약자. 패키지/permission/instrumentation/feature 목록, 패키지 설치/제거 등
am : activity manager의 약자, 액티비티 시작, Intent 브로드캐스팅, Instrumentation 시작, profiling 시작 / 중지 등
service : 안드로이드 서비스 목록 표시, 서비스에 명령 전달
monkey : 애플리케이션에 랜덤 이벤트 발생시킴. 사용자 이벤트, 시스템 이벤트의 무작위 발행
cat /data/anr/traces.txt : VM TRACES (쓰레드 덤프)
cat /proc/binder/proc/[PID] : 바인더 프로세스 상태
cat /proc/binder/xxx : 바인더 관련 정보(xxx은 transaction, transaction_log, failed_transaction_log, stats 등)
cat /data/system/packages.xml : 설치된 패키지 세팅 정보
setprop : system property 셋팅
getprop : 셋팅된 system property 목록 출력


종합 리포트 
dumpsys [service] : app/service 상태정보 덤프, 서비스별로 추가 파라미터 받을 수 있음
dumpstate : device 상태정보 덤프. 상태정보를 추출하는 여러 명령어들의 조합으로 구성
dumpcrash : 애플리케이션이 crach될 때의 상태정보 덤프
bugreport : logcat + dumpsys + dumpstat

그 밖에...
그 밖의 안드로이드 shell 명령어는 /system/bin 및 /system/xbin을 뒤져보면 많이 나온다. 이제 남은 일은 찾아낸 명령어의 사용법, 출력결과를 어떻게 해석할지, 어떤 상황에서 이들을 활용할지 사례조사, 그리고 직접 활용해보는 것이다.
Posted by 파이드라
```
