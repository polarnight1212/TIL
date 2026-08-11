# Day 3 — Linux와 로봇 개발 환경

> **주제:** Linux / Ubuntu와 로봇 개발 환경 이해
> **핵심 키워드:** Ubuntu, ROS 2, Kernel, User Space, Process, File System, Permission, Bash, SSH, udev

---

## 1. 로봇 개발의 기본 환경

로봇을 개발할 때 가장 많이 사용하는 운영체제는 **Linux 계열**, 그중에서도 **Ubuntu**이다.

그리고 로봇 소프트웨어 개발에서는 **ROS 2(Robot Operating System 2)**가 핵심적인 미들웨어 및 개발 프레임워크로 사용된다.

따라서 로봇 개발자의 기본적인 개발 환경은 다음과 같이 볼 수 있다.

```text
Ubuntu Linux
    ↓
ROS 2
    ↓
Python / C++
    ↓
센서 / 카메라 / LiDAR / 모터 / 로봇 하드웨어
```

---

# 2. 왜 Linux인가?

Linux는 로봇 개발에서 사실상 기본적인 개발 환경으로 사용된다.

## 2.1 오픈소스

Linux는 오픈소스 운영체제이다.

소스 코드가 공개되어 있기 때문에 다양한 개발자와 기업이 자유롭게 수정하고 활용할 수 있다.

또한 Ubuntu와 같은 Linux 배포판은 무료로 사용할 수 있다.

---

## 2.2 가볍고 조립 가능한 구조

Linux는 필요한 기능을 선택하고 조합하여 사용할 수 있다.

로봇처럼 제한된 컴퓨팅 자원을 사용하는 시스템에서는 불필요한 기능을 줄이고 필요한 기능을 구성하는 것이 중요하다.

---

## 2.3 강력한 원격 접속 및 자동화

Linux는 터미널 기반 작업과 원격 접속 기능이 매우 강력하다.

대표적으로 다음과 같은 도구가 있다.

* Bash
* SSH
* Shell Script
* systemd
* cron
* udev

특히 실제 로봇은 모니터나 키보드를 연결하지 않고 사용하는 경우가 많기 때문에 **SSH를 이용한 원격 개발**이 중요하다.

---

## 2.4 강력한 개발 생태계

로봇 개발에 필요한 다양한 오픈소스 소프트웨어가 Linux 환경을 중심으로 제공된다.

대표적인 예:

* ROS 2
* OpenCV
* PyTorch
* Gazebo
* RViz2
* NumPy
* CUDA

따라서 로봇 개발자는 Linux 환경에 익숙해지는 것이 중요하다.

---

# 3. Linux의 구조

Linux는 크게 다음과 같이 나누어 생각할 수 있다.

```text
┌──────────────────────────────┐
│       사용자 프로그램         │
│                              │
│ Bash / Python / ROS 2 / RViz2│
│                              │
│          User Space          │
└──────────────┬───────────────┘
               │
          System Call
               │
┌──────────────▼───────────────┐
│            Kernel             │
│                              │
│ 프로세스 스케줄링             │
│ 메모리 관리                  │
│ 디바이스 드라이버             │
│ 파일 시스템                  │
│ 네트워크                     │
└──────────────┬───────────────┘
               │
┌──────────────▼───────────────┐
│           Hardware            │
│                              │
│ CPU / RAM / USB / Camera     │
│ LiDAR / Sensor / Motor       │
└──────────────────────────────┘
```

Linux 시스템은 크게 **Kernel(커널)**과 **User Space(사용자 공간)**로 나눌 수 있다.

---

## 3.1 Kernel

Kernel은 운영체제의 핵심 부분이다.

주요 역할은 다음과 같다.

* 프로세스 스케줄링
* 메모리 관리
* 디바이스 드라이버 관리
* 파일 시스템 관리
* 네트워크 관리
* 하드웨어 자원 관리

즉, 프로그램과 하드웨어 사이에서 자원을 관리하는 역할을 한다.

---

## 3.2 User Space

사용자가 직접 실행하는 프로그램들은 일반적으로 User Space에서 실행된다.

예:

* Bash
* Python
* ROS 2 Node
* RViz2
* Gazebo
* OpenCV 프로그램

사용자 프로그램이 하드웨어에 직접 접근하는 것이 아니라 **Kernel을 통해 하드웨어에 접근**한다.

---

# 4. System Call

프로그램이 파일을 읽거나 USB 장치에 접근하려면 Kernel에 요청해야 한다.

이때 사용하는 인터페이스가 **System Call(시스템 콜)**이다.

```text
Python 프로그램
      │
      │ 시스템 콜
      ▼
    Kernel
      │
      │ Device Driver
      ▼
    Hardware
```

예를 들어 Python 프로그램이 파일을 읽는다고 생각해보자.

```text
Python
  ↓
파일 읽기 요청
  ↓
System Call
  ↓
Kernel
  ↓
File System
  ↓
Storage
```

USB 장치를 사용하는 경우에도 비슷하다.

```text
ROS 2 Node
   ↓
System Call
   ↓
Kernel
   ↓
Device Driver
   ↓
USB Device
```

---

# 5. Kernel과 Device Driver

Kernel은 **디바이스 드라이버(Device Driver)**를 통해 하드웨어를 제어한다.

```text
Application
     ↓
System Call
     ↓
Kernel
     ↓
Device Driver
     ↓
Hardware
```

이 구조의 중요한 장점은 **프로그램이 하드웨어를 직접 제어하지 않는다는 것**이다.

따라서 특정 프로그램에 문제가 발생하더라도 운영체제 전체에 영향을 주는 것을 어느 정도 방지할 수 있다.

예를 들어 ROS 2 노드 하나가 종료되더라도 다른 ROS 2 노드와 운영체제 전체가 계속 실행될 수 있다.

---

# 6. Process(프로세스)

## 6.1 프로세스란?

**프로세스(Process)**는 실행 중인 프로그램 하나하나를 의미한다.

예를 들어 다음 프로그램들이 실행되고 있다면 각각 하나의 프로세스가 될 수 있다.

```text
Bash
Python
ROS 2 Node
RViz2
Gazebo
```

각 프로세스는 **PID(Process ID)**라는 고유 번호를 가진다.

```text
Process
 ├── PID
 ├── Memory
 ├── Resources
 └── Execution State
```

PID는 다음 명령어로 확인할 수 있다.

```bash
ps aux
```

---

## 6.2 프로세스의 메모리 격리

프로세스는 기본적으로 독립적인 메모리 공간을 할당받는다.

따라서 하나의 프로세스가 다른 프로세스의 메모리를 마음대로 침범할 수 없다.

```text
┌──────────────┐
│ Process A    │
│ Memory A     │
└──────────────┘

┌──────────────┐
│ Process B    │
│ Memory B     │
└──────────────┘
```

이러한 **프로세스 간 격리**는 시스템의 안정성을 높여준다.

---

# 7. CPU 스케줄링과 지터(Jitter)

CPU에서는 여러 프로세스가 동시에 실행되는 것처럼 보이지만 실제로는 Kernel의 **스케줄러(Scheduler)**가 CPU 시간을 프로세스들에게 배분한다.

```text
             Kernel Scheduler
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
    ROS Node      Python       RViz2
       │            │            │
       └────── CPU Time ─────────┘
```

이 과정에서 프로세스가 정확히 원하는 순간에 CPU를 할당받지 못하면 **실행 시간의 변동**이 발생할 수 있다.

이러한 시간적 변동을 **Jitter(지터)**라고 한다.

특히 로봇 제어 시스템에서는 일정한 주기로 센서 데이터를 처리하거나 모터 명령을 보내야 하기 때문에 지터가 중요하다.

---

# 8. ROS 2와 프로세스 격리

ROS 2에서는 여러 기능을 각각 Node로 나누어 개발하는 경우가 많다.

예:

```text
LiDAR Node
     │
     ├── /scan
     │
Camera Node
     │
     ├── /image
     │
SLAM Node
     │
     ├── /map
     │
Navigation Node
     │
     └── /cmd_vel
```

각 Node가 독립적인 프로세스로 실행될 수 있기 때문에 하나의 프로그램에 문제가 발생하더라도 전체 시스템을 구성하는 다른 프로그램들은 계속 실행될 수 있다.

> **ROS 2 시스템의 견고함은 프로세스 간 격리와 분산 구조에서 중요한 이점을 얻는다.**

---

# 9. Linux 파일 시스템

Linux에서는 **"모든 것은 파일이다(Everything is a file)"**라는 철학이 중요하다.

Linux의 파일 시스템은 루트(`/`)에서 시작하는 하나의 트리 구조로 되어 있다.

```text
/
├── bin
├── boot
├── dev
├── etc
├── home
├── lib
├── opt
├── tmp
├── usr
└── var
```

---

# 10. 장치 파일(Device File)

Linux에서는 하드웨어 장치도 파일처럼 표현된다.

대표적인 장치 파일:

```text
/dev/
```

예:

```text
/dev/ttyUSB0
/dev/ttyACM0
/dev/video0
```

이러한 장치 파일을 통해 프로그램은 하드웨어와 데이터를 주고받을 수 있다.

예를 들어:

```text
/dev/ttyUSB0
      ↓
USB Serial Device
      ↓
LiDAR
```

카메라는 다음과 같이 표현될 수 있다.

```text
/dev/video0
      ↓
Camera
```

---

## 10.1 장치 파일의 장점

하드웨어를 파일처럼 다룰 수 있다는 것은 매우 강력한 개념이다.

일반 파일을 읽고 쓰는 방식과 비슷한 방식으로 장치와 데이터를 주고받을 수 있다.

```text
일반 파일
open → read → write → close

장치 파일
open → read → write → close
```

따라서 하드웨어를 다루는 코드가 파일을 다루는 코드와 유사해진다.

---

# 11. Linux 경로

Linux에서는 파일이나 디렉터리를 **경로(Path)**로 표현한다.

두 가지 방식이 있다.

## 11.1 절대 경로

루트(`/`)부터 시작하는 경로이다.

```bash
/home/robot/project/main.py
```

항상 `/`부터 시작한다.

---

## 11.2 상대 경로

현재 작업 디렉터리를 기준으로 표현하는 경로이다.

```bash
./main.py
```

또는

```bash
../project/main.py
```

주요 기호:

| 기호   | 의미             |
| ---- | -------------- |
| `/`  | 루트             |
| `.`  | 현재 디렉터리        |
| `..` | 상위 디렉터리        |
| `~`  | 현재 사용자의 홈 디렉터리 |

---

# 12. Linux 사용자와 권한

Linux는 **다중 사용자 시스템**이다.

따라서 파일에는 소유자와 권한이 존재한다.

권한의 기본 구조는 다음과 같다.

```text
사용자(User)   → u
그룹(Group)    → g
그 외(Others)  → o
```

권한은 다음 세 가지로 구성된다.

```text
r = read
w = write
x = execute
```

---

# 13. 파일 권한 구조

예를 들어 다음과 같은 권한이 있다고 하자.

```text
-rwxr-xr--
```

이를 다음과 같이 나눌 수 있다.

```text
- | rwx | r-x | r--
  |     |     |
  |     |     └── Others
  |     └──────── Group
  └────────────── Owner
```

각 권한은 다음과 같다.

| 권한  | 의미 |
| --- | -- |
| `r` | 읽기 |
| `w` | 쓰기 |
| `x` | 실행 |

---

## 13.1 파일 종류

권한 앞의 첫 번째 문자는 파일 종류를 나타낸다.

| 기호  | 의미                      |
| --- | ----------------------- |
| `-` | 일반 파일                   |
| `d` | 디렉터리                    |
| `c` | 문자 장치(Character Device) |

예:

```text
-rwxr-xr--
```

일반 파일

```text
drwxr-xr-x
```

디렉터리

---

## 13.2 권한 예시

다음과 같은 경우를 생각해보자.

```text
rwx
```

소유자는:

```text
읽기 + 쓰기 + 실행
```

가능하다.

```text
r-x
```

그룹은:

```text
읽기 + 실행
```

가능하고 쓰기는 불가능하다.

```text
r--
```

그 외 사용자는:

```text
읽기
```

만 가능하다.

---

# 14. chmod

`chmod`는 파일이나 디렉터리의 권한을 변경하는 명령어이다.

예:

```bash
chmod +x run.sh
```

`run.sh`에 실행 권한을 추가한다.

그 후 다음과 같이 실행할 수 있다.

```bash
./run.sh
```

---

# 15. chown

`chown`은 파일이나 디렉터리의 소유자를 변경하는 명령어이다.

기본 형태:

```bash
chown 사용자 파일
```

예:

```bash
sudo chown robot:robot run.sh
```

---

# 16. sudo

`sudo`는 일반 사용자가 **관리자(root) 권한으로 명령을 실행**할 수 있도록 해주는 명령어이다.

예:

```bash
sudo apt update
```

또는:

```bash
sudo nano /etc/udev/rules.d/99-robot.rules
```

---

## 16.1 sudo를 무조건 사용하는 습관의 위험성

명령어에서 권한 오류가 발생했다고 해서 무조건 `sudo`를 붙이는 것은 위험하다.

예:

```bash
command
```

에서 권한 오류가 발생했다고 해서 바로

```bash
sudo command
```

로 실행하는 습관은 피해야 한다.

왜냐하면 root 권한으로 실행하면 시스템 전체에 영향을 줄 수 있기 때문이다.

특히 다음과 같은 작업은 주의해야 한다.

```text
파일 삭제
시스템 설정 변경
장치 설정 변경
패키지 설치
권한 변경
udev 설정
```

> **권한 오류가 발생하면 먼저 왜 권한이 필요한지 확인하고 sudo를 사용한다.**

---

# 17. 시리얼 포트 접근 권한

로봇 개발에서는 USB Serial 장치를 자주 사용한다.

예:

```text
USB
 ↓
Serial
 ↓
LiDAR / Motor Controller / Microcontroller
```

대표적인 장치 파일:

```bash
/dev/ttyUSB0
/dev/ttyACM0
```

이때 가장 흔하게 발생하는 문제 중 하나가 **시리얼 포트 접근 권한 문제**이다.

예:

```text
Permission denied
```

이 경우 단순히 `sudo`로 해결하기보다는 사용자를 적절한 그룹에 추가하는 등의 방법을 사용하는 것이 좋다.

---

# 18. Shell과 Bash

## 18.1 Shell이란?

**Shell(셸)**은 사용자가 입력한 명령어를 해석하고 운영체제에 전달하는 프로그램이다.

```text
사용자
  ↓
Shell
  ↓
Kernel
  ↓
Hardware
```

Ubuntu의 대표적인 기본 Shell은 **Bash(Bourne Again SHell)**이다.

---

# 19. Bash 필수 명령어

로봇 개발에서 자주 사용하는 명령어를 기능별로 정리하면 다음과 같다.

## 19.1 디렉터리 탐색

### 현재 위치 확인

```bash
pwd
```

### 파일 목록 확인

```bash
ls
```

### 자세한 정보 확인

```bash
ls -l
```

### 디렉터리 이동

```bash
cd
```

예:

```bash
cd ~/robot_ws
```

---

# 20. 파일 및 디렉터리 관리

### 파일 복사

```bash
cp
```

예:

```bash
cp main.py backup.py
```

### 파일 이동 / 이름 변경

```bash
mv
```

예:

```bash
mv main.py robot.py
```

### 파일 및 디렉터리 삭제

```bash
rm
```

디렉터리를 포함하여 삭제:

```bash
rm -r directory
```

> `rm -r`은 파일을 실제로 삭제하므로 주의해서 사용한다.

### 디렉터리 생성

```bash
mkdir
```

중간 디렉터리까지 한 번에 생성:

```bash
mkdir -p ~/robot_ws/src
```

---

# 21. 파일 내용 확인

### 전체 내용 출력

```bash
cat file.txt
```

### 페이지 단위로 확인

```bash
less file.txt
```

### 앞부분 확인

```bash
head file.txt
```

### 뒷부분 확인

```bash
tail file.txt
```

### 실시간으로 파일 변경 내용 확인

```bash
tail -f file.log
```

`tail -f`는 로그 파일을 실시간으로 확인할 때 매우 유용하다.

---

# 22. 검색 명령어

## grep

특정 패턴이 포함된 내용을 검색한다.

```bash
grep -r "패턴" 경로
```

예:

```bash
grep -r "ERROR" ~/robot_ws
```

---

## find

파일이나 디렉터리를 검색한다.

예:

```bash
find . -name "*.py"
```

현재 디렉터리 아래에서 `.py` 파일을 찾는다.

---

# 23. 프로세스 관리

## 현재 실행 중인 프로세스 확인

```bash
ps aux
```

많은 프로세스를 확인하거나 실시간으로 모니터링하려면:

```bash
htop
```

---

## 프로세스 종료

```bash
kill PID
```

예:

```bash
kill 12345
```

강제로 종료해야 할 경우:

```bash
kill -9 12345
```

> `kill -9`는 프로세스가 정리 작업을 수행할 기회를 주지 않고 강제로 종료하므로 일반적인 종료 방법으로 남용하지 않는 것이 좋다.

---

# 24. 장치 및 시스템 확인

## USB 장치 확인

```bash
lsusb
```

현재 연결된 USB 장치를 확인할 수 있다.

---

## Kernel 로그 확인

```bash
dmesg | tail
```

USB 장치를 연결하거나 하드웨어 문제가 발생했을 때 Kernel 로그를 확인하는 데 유용하다.

---

## 디스크 사용량 확인

```bash
df -h
```

디스크의 전체 용량과 사용량을 사람이 읽기 쉬운 형태로 보여준다.

---

# 25. Linux 명령어 조합

Linux에서는 여러 명령어를 조합해서 사용하는 것이 매우 중요하다.

대표적인 연산자는 다음과 같다.

| 기호   | 의미                 |     |
| ---- | ------------------ | --- |
| `    | `                  | 파이프 |
| `>`  | 출력 리다이렉션           |     |
| `&&` | 앞 명령 성공 시 다음 명령 실행 |     |

---

## 25.1 Pipe `|`

파이프는 한 명령어의 결과를 다음 명령어의 입력으로 전달한다.

예:

```bash
dmesg | tail
```

의미:

```text
dmesg
  ↓
출력
  ↓
tail
  ↓
마지막 부분 출력
```

Linux의 핵심 철학 중 하나인

> **작은 도구를 조합해서 복잡한 작업을 수행한다.**

를 잘 보여주는 기능이다.

---

## 25.2 `>`

명령어의 출력을 파일로 저장할 수 있다.

```bash
ls > files.txt
```

---

## 25.3 `&&`

앞의 명령이 성공했을 때 다음 명령을 실행한다.

```bash
command1 && command2
```

예:

```bash
sudo apt update && sudo apt upgrade
```

---

# 26. SSH

**SSH(Secure Shell)**는 원격 Linux 시스템에 접속하기 위한 표준적인 방법이다.

특히 모니터나 키보드가 연결되지 않은 **헤드리스(Headless) 로봇**을 관리할 때 매우 중요하다.

```text
내 PC
  │
  │ SSH
  ▼
로봇 PC
```

---

## 26.1 IP 주소로 접속

```bash
ssh robot@192.168.0.42
```

---

## 26.2 호스트 이름으로 접속

mDNS 등을 이용하면 IP 주소 대신 호스트 이름을 사용할 수 있다.

```bash
ssh robot@myrobot.local
```

---

# 27. SSH의 보안 원리

SSH는 암호화된 통신을 제공한다.

특히 공개키/개인키 방식의 인증을 사용할 수 있다.

```text
내 PC
┌──────────────────┐
│ Private Key      │
│ 개인키            │
└──────────────────┘
          │
          │ SSH
          ▼
┌──────────────────┐
│ Robot            │
│ Public Key       │
│ 공개키            │
└──────────────────┘
```

개념적으로 서버는 공개키를 이용해 인증 과정을 수행하고, 개인키를 가진 클라이언트만 이에 대응할 수 있도록 한다.

따라서 개인키 자체를 네트워크로 전송하지 않고 안전하게 인증할 수 있다.

---

## 27.1 SSH Key의 장점

* 비밀번호를 직접 입력하지 않고 인증 가능
* 자동화에 유리
* Shell Script와 함께 사용하기 좋음
* 로봇 여러 대를 원격으로 관리하기 편리
* 암호화된 통신 제공

따라서 실제 로봇 시스템에서는 SSH Key 기반 접근이 매우 유용하다.

---

# 28. udev Rules

로봇을 개발하다 보면 USB 장치의 이름이 바뀌는 문제가 발생할 수 있다.

예를 들어 LiDAR가 연결되었을 때:

```text
/dev/ttyUSB0
```

였다가 다른 USB 장치를 먼저 연결하면:

```text
/dev/ttyUSB1
```

이 될 수도 있다.

이런 상황에서는 ROS 2 노드나 Python 프로그램에서 장치 경로가 바뀌기 때문에 문제가 발생할 수 있다.

이 문제를 해결하는 방법 중 하나가 **udev rules**이다.

---

# 29. udev란?

udev는 Linux Kernel의 장치 이벤트를 관리하는 장치 관리 시스템이다.

udev rule을 사용하면 특정 특징을 가진 장치가 연결되었을 때 원하는 이름의 링크를 만들도록 설정할 수 있다.

개념적으로는 다음과 같다.

```text
특정 조건을 만족하는 장치가 연결되면
            ↓
udev rule 확인
            ↓
지정한 이름의 장치 링크 생성
```

예:

```text
USB LiDAR
    ↓
idVendor = 10c4
idProduct = ea60
    ↓
udev rule
    ↓
/dev/lidar
```

이렇게 하면 프로그램에서는 다음처럼 고정된 이름을 사용할 수 있다.

```bash
/dev/lidar
```

---

# 30. udev Rule 설정 과정

## Step 1. 장치의 고유 정보 확인

다음 명령어를 사용한다.

```bash
udevadm info -a -n /dev/ttyUSB0 | grep -E "idVendor|idProduct|serial" | head -3
```

이를 통해 장치의 고유 식별 정보를 확인할 수 있다.

주요 정보:

```text
idVendor
idProduct
serial
```

---

# 31. Step 2. udev Rule 작성

규칙 파일을 생성한다.

```bash
sudo nano /etc/udev/rules.d/99-robot.rules
```

예를 들어 다음 조건을 가진 장치를 `/dev/lidar`라는 이름으로 연결한다고 하자.

```text
idVendor  = 10c4
idProduct = ea60
```

Rule:

```bash
SUBSYSTEM=="tty", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", SYMLINK+="lidar", MODE="0666"
```

각 부분의 의미:

| 항목                         | 의미                 |
| -------------------------- | ------------------ |
| `SUBSYSTEM=="tty"`         | tty 장치 대상          |
| `ATTRS{idVendor}=="10c4"`  | 제조사 ID가 10c4       |
| `ATTRS{idProduct}=="ea60"` | 제품 ID가 ea60        |
| `SYMLINK+="lidar"`         | `/dev/lidar` 링크 생성 |
| `MODE="0666"`              | 장치 파일 권한 설정        |

---

# 32. Step 3. udev Rule 적용

규칙을 다시 불러온다.

```bash
sudo udevadm control --reload-rules
```

그리고 장치 이벤트를 다시 발생시킨다.

```bash
sudo udevadm trigger
```

두 명령어를 한 번에 실행할 수도 있다.

```bash
sudo udevadm control --reload-rules && sudo udevadm trigger
```

---

# 33. udev의 로봇 개발 활용

udev는 로봇에서 센서와 컨트롤러를 안정적으로 연결하기 위해 매우 유용하다.

예:

```text
/dev/ttyUSB0
/dev/ttyUSB1
/dev/ttyUSB2
```

처럼 장치 번호에 의존하는 대신,

```text
/dev/lidar
/dev/motor
/dev/gps
```

처럼 의미 있는 이름을 사용할 수 있다.

ROS 2나 Python 코드에서도 고정된 장치 경로를 사용할 수 있다.

예:

```python
lidar_port = "/dev/lidar"
```

이렇게 하면 USB 연결 순서가 달라져도 프로그램에서 사용하는 경로를 일정하게 유지할 수 있다.

---

# 34. 오늘 배운 내용을 로봇 관점에서 연결하기

지금까지 배운 Linux 개념을 실제 로봇 시스템에 연결하면 다음과 같다.

```text
                    Ubuntu Linux
                         │
                    ┌────▼────┐
                    │ Kernel  │
                    └────┬────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
     Process           Device          Network
        │                │                │
        │              udev              SSH
        │                │                │
     ROS 2 Node       /dev/ttyUSB0      Remote PC
        │                │
        │              LiDAR
        │
   ┌────┼────┬─────┐
   ↓    ↓    ↓     ↓
 LiDAR Camera SLAM Navigation
 Node   Node  Node   Node
```

결국 로봇 소프트웨어 개발자는 단순히 Python이나 C++ 코드만 작성하는 것이 아니라 다음과 같은 시스템을 함께 이해해야 한다.

```text
Linux
 ├── Process
 ├── Memory
 ├── File System
 ├── Permission
 ├── Device
 ├── Network
 ├── Shell
 ├── SSH
 └── udev
       ↓
     ROS 2
       ↓
 Robot Software
       ↓
 Robot Hardware
```

---

# 35. 오늘의 핵심 정리

## Linux

* 로봇 개발의 기본 운영체제
* 오픈소스
* 가볍고 조립 가능한 구조
* 강력한 원격 접속 및 자동화
* ROS 2, OpenCV, PyTorch 등과 높은 호환성

## Kernel

* 프로세스 관리
* 메모리 관리
* 디바이스 드라이버
* 파일 시스템
* 하드웨어 자원 관리

## User Space

* Bash
* Python
* ROS 2 Node
* RViz2
* Gazebo

## Process

* 실행 중인 프로그램
* PID를 가짐
* 독립적인 메모리 공간
* Kernel Scheduler가 CPU 시간 배분
* 실행 시간 변동으로 Jitter가 발생할 수 있음

## File System

* `/`에서 시작하는 하나의 트리 구조
* "Everything is a file"
* 하드웨어 장치도 `/dev` 아래의 장치 파일로 표현

## Permission

```text
u = user
g = group
o = others

r = read
w = write
x = execute
```

주요 명령어:

```bash
chmod
chown
sudo
```

## Bash

주요 명령어:

```bash
pwd
ls
cd
cp
mv
rm
mkdir
cat
less
head
tail
grep
find
ps
htop
kill
lsusb
dmesg
df
```

## SSH

* 헤드리스 로봇에 원격 접속
* 암호화된 통신
* 공개키/개인키 인증
* 자동화에 유용

## udev

* USB/Serial 장치의 이름을 안정적으로 관리
* 장치의 Vendor ID / Product ID / Serial 등을 기준으로 식별
* `/dev/lidar`와 같은 고정된 이름을 만들 수 있음

---

# 36. Linux 명령어 Cheat Sheet

| 목적          | 명령어                              |
| ----------- | -------------------------------- |
| 현재 경로       | `pwd`                            |
| 파일 목록       | `ls -l`                          |
| 디렉터리 이동     | `cd`                             |
| 파일 복사       | `cp`                             |
| 파일 이동/이름 변경 | `mv`                             |
| 삭제          | `rm`                             |
| 디렉터리 생성     | `mkdir -p`                       |
| 파일 내용       | `cat`                            |
| 페이지 단위 확인   | `less`                           |
| 앞부분         | `head`                           |
| 뒷부분         | `tail`                           |
| 실시간 로그      | `tail -f`                        |
| 문자열 검색      | `grep`                           |
| 파일 검색       | `find`                           |
| 프로세스 확인     | `ps aux`                         |
| 프로세스 모니터링   | `htop`                           |
| 프로세스 종료     | `kill`                           |
| 강제 종료       | `kill -9`                        |
| USB 확인      | `lsusb`                          |
| Kernel 로그   | `dmesg`                          |
| 디스크 확인      | `df -h`                          |
| 권한 변경       | `chmod`                          |
| 소유자 변경      | `chown`                          |
| 관리자 권한      | `sudo`                           |
| 원격 접속       | `ssh`                            |
| udev 정보 확인  | `udevadm info`                   |
| udev 규칙 재로드 | `udevadm control --reload-rules` |
| udev 이벤트 적용 | `udevadm trigger`                |

---

# 37. 오늘의 핵심 한 문장

> **로봇 개발자는 ROS 2만 배우는 것이 아니라, ROS 2가 실행되는 Linux 시스템 자체를 이해해야 한다.**

Linux의 **Process, File System, Permission, Device, Network, Shell, SSH, udev**를 이해하면 ROS 2에서 발생하는 많은 문제를 더 깊게 이해하고 해결할 수 있다.
