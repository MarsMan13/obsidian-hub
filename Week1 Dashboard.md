# 1주차 대시보드

# Goal of this week

1. Firmware를 Fuzzing이나 정적분석기와 같은 자동화 툴을 이용해서 분석하겠다.

2. 가능하다면, Greybox Fuzzing을 이용하여 취약점을 찾을 것이고,

Firmware가상화와 같은 이슈가 해결되지 않는다면, 정적분석 또한 고려해보자

3. 따라서, 이번주의 목표는

제한된 프로젝트 기간 동안 최대한 많은 취약점을 찾을 수 있는 자동화 툴을 선택하기 위한 자료조사이다.

  

# Todo: How to achieve my goal of this week?

  

1. 먼저 Firmware가 무엇인지를 취약점 분석의 관점에서 확인

2. 기존에 Firmware를 어떻게 자동화 분석해왔는 지를 구글링(실무)과 논문(이론)을 중심으로 찾아보고, 공부하자

3. 우리가 처음 제시했던, Greybox Fuzzing을 우선으로 하기에

가능하다면 아무 펌웨어에 대해 Fuzzing해본다.

여기서 Fuzzing이란, Firmware Fuzzing : (Firmware) → (Crash)로서,

그 과정에는 가상화와 같은 Fuzzing 환경 구성을 포함한다.

  

# Work space

  

## 전제

  

1. Firmware가 있다.

2. Firmware는 바이너리 형태로서, 소스코드가 부재하다.

3. Firmware 암호화는 해제되어 있다.

4. Firmware 자동화툴로 취약점을 분석하자

  

## 배경지식: IoT 펌웨어

  

펌웨어는

  

- Boot loader : 시스템의 booting시 가장 먼저 로드되어 실행됨 ⇒ 하드웨어 초기화, 메모리 검사, 기본 설정 로딩 등 수행

- Kernel : 하드웨어와 소프트웨어 간의 인터페이스로서, 프로세스/메모리/입출력/파일시스템/네트워크/보안및엑세스 관리를 담당

- File system : Kernel이 secondary memory에 저장된 파일에 접근하기 위한 인터페이스이자,

ROM 등의 저장 매체에 어떻게 구조화하고 저장할 것인지에 대한 규칙을 통칭

  

등으로 이뤄짐

  

⇒ 이를 보면, 사실 펌웨어 자체를 Fuzzing하는 방법이 가장 확실하긴 하지만

  

내부를 들여다본 후에 핵심적인 프로그램만을 Fuzzing하는 방법도 좋지않을까 싶다!

  

하지만, 이같은 경우에도 역시나 PUT과 하드웨어와의 종속성이 존재하기 때문에 이를 처리하는 게 쉽지 않을 것 같으며

  

이를 처리한다는 것은 가상화를 의미하며, 지금 생각하기로는 가상화가 가장 큰 허들이기에

  

펌웨어를 Fuzzing하나, 각각의 프로그램을 Fuzzing하나 가상화라는 큰 허들을 넘어야하는 것은 매한기라 생각한다.

  

⇒ 결론. Fuzzing을 선택할 시, 어찌되었든지 가상화가 가장 큰 문제이다.

  

### Task: 펌웨어 뜯어보기 with binwalk

  

- Who : 규원님이 주신 블랙박스 기기의 펌웨어.fex

- What : 해당 펌웨어를 간단하게 분석해보며, 펌웨어를 느껴보자

- How : binwalk를 사용해서!

binwalk란? 펌웨어 분석 도구로,

펌웨어 이미지 내의 파일 시스템, 압축 영역, 실행 가능한 코드 등의 구성 요소를

자동으로 탐지하고 추출할 수 있는 기능을 제공

- Output :

**1. 파일 시그니처를 통한, 구성요소 확인**

    ![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%84%87%E1%85%A9%E1%84%83%E1%85%B3%200525486a26224ab9abb15879e2961ab9/Untitled.png)

    - Boot loader는 Android bootimg,

    - Kernel은 Linux kernel ARM boot executable zImage, gzip compressed data,

    - File system은 Squashfs : 읽기 전용의 압축 파일 시스템 ⇒ 압축, 읽기 전용, 효율적인 블록 alignmentation, 빠른 마운트 속도

    JFFS2 filesystem : 플래시 메모리용 파일 시스템

    - 기타 : CRC32(오류 검사에 쓰임), JPEG image data, TIFF image data

    1. **펌웨어 파일 추출(-e option)**

        ![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%84%87%E1%85%A9%E1%84%83%E1%85%B3%200525486a26224ab9abb15879e2961ab9/Untitled%201.png)

        위와 같이 squashfs-root라는 디렉토리로 squashfs 파일시스템이 추줄도니 것을 알 수 있다. (jffs2의 경우, jefferson을 사용해서 추출해야함!)

        ![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%84%87%E1%85%A9%E1%84%83%E1%85%B3%200525486a26224ab9abb15879e2961ab9/Untitled%202.png)

  

## 자료조사: Firmware 자동화 분석에 대하여

  

### Fuzzing을 중심으로

  

- [Firmware Fuzzing: The State of the Art](https://dl.acm.org/doi/fullHtml/10.1145/3457913.3457934) (Literature Review Paper) 읽기 및 생각

    1. Abstract:

        1. Due to the limited resouces, IoT cannot deploy sophisticated run-time protection techniques

        2. The reasons that Fuzzing Firmware is difficult :

        1) The size and complexity of firmware

        2) The variety of firmware types

        3. Device-based fuzzing VS Simulation-based fuzzing that is the mainstream in the future

    2. Introduction

        1. There are many methods for testing firmware:

        1) static analysis

        2) symbolic execution

        3) fuzzing

        4) machine-learning based vulnerabilities prediction

            ![azZWY.png](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%84%87%E1%85%A9%E1%84%83%E1%85%B3%200525486a26224ab9abb15879e2961ab9/azZWY.png)

    3. Research questions

        Q1) Execution environment of firmware fuzzing

        Q2) Main types of vulnerabilies

        Q3) Diffence between firmware fuzzing and general software fuzzing

    4. Q1: Execution environment

        1. Target types

        1) complete firmware

        2) firmware system

        3) firmware communication protocol

        4) firmware unpacking files

        2. Simulation-based fuzzing replies on simulator such as QEMU. But To simulate peripherals is difficult.

        So, current reseach focues on using different methods to make up for this deficiency.

        3. Notable emulators

        1) Avatar1, 2

        2) FIRMANYNE base of FIRM-AFL

        3) ~~FirmAE~~

        4)  ~~Laelaps~~

        3) HALucinator : Decompling HW and Firmware by deploying HAL layer

    5. Q2: Main types of vulnerablilies

        ![Untitled](1%E1%84%8C%E1%85%AE%E1%84%8E%E1%85%A1%20%E1%84%83%E1%85%A2%E1%84%89%E1%85%B5%E1%84%87%E1%85%A9%E1%84%83%E1%85%B3%200525486a26224ab9abb15879e2961ab9/Untitled%203.png)

    6. Q3: More difficulties than general software fuzzing

        1. The difficulty of obtaining binary files of firmware

        2. The execution environment of firmware is difficult to build : Getting proper infos from firmware durning its execution

        3. Firmware binary is more complicated due to

        1) Direct connection with HW

        2) optimization due to limited resources

        3) Variety peripherals

        4. Firmware input is more compilicated.

        Firmware input types are an input from the perception layer (such as video, sound sensors) and an input from the transmission layer(wireless, Bluetooth, etc)

        and interrupt ( its value, timing )

        ⇒ Integrating Static analysis, Symbolic execution, and other techniqeus is also hardㅠㅠ

    7. Discussions

        1. Manually modifying the simulator to add support for other HW is a difficult and time-consuming work

    ### 해당 논문을 읽고

    부끄럽지만 처음 생각한 “IoT Firmware에 대한 Directed Greybox Fuzzing”이라는 소리가 얼마나 허무맹랑한 소리인지를 깨달았다.

    무식하니 용감했다.

    2020년 논문이긴하지만, 당신 기술로는 Firmware에 대한 Directed는 커녕, Greybox Fuzzing도 되지 않고 있었으며,

    완벽한 가상화(Complete Simulation Environment)라는 최우선 목표가 더 중요했다.

    각설.

    때문에 우리의 전략은 새로운 기술을 만들기엔 시간이 많이 부족하기에,

    최최신 기술 && 우리 Firmware와 알맞는 기술을 가져다 써보는데 열과 성을 다해야겠다고 생각했다.

- [firm-afl](https://www.usenix.org/conference/usenixsecurity19/presentation/zheng) 논문 읽기 및 생각

    1. Abstract

        The Motivations of this paper and the fundamental problems in IoT fuzzing it intended to solve

        1) Addressing compatible issues by enabling fuzzing for POSIX

        2) Performance bottleneck  caused by system-mode of QEMU.

        3) Fully functional ~ ⇒ Really?

    2. Introduction

        1. Challenges in IoT firmware fuzzing

        1) Its strong dependency on the actual HW config.

        2) Slower than a general fuzzing machine : Enormous runtime overhead from implemented MMU and System Call

        2. Our solution : Full-system emulation + User-mode emulation = Augmented process emulation ⇒ Generality, Efficiency

        ⇒ AFL + Firmadyne

    3. Background and Motivation

        1. Fuzzing

        For most IoT devices, source codes and design documentations are absense

        ⇒ Instrument the program dynamically

        2. QEMU

        GVA = HVA + OFFSET ⇒ User mode is much faster then System mode

        3. Testing IoT Firmware

            1) Main challenges : Compatibility, Code coverage

            2) Avatar : Makes Proxy between the emulator and the real hardware ⇒ Using HW ⇒ Slow

            3) IoTFuzzer : Blackbox fuzzing directly on the real device ⇒ Slow (15cases per sec)

            4) Firmadyne : Fully emulates the system.

            5) AFL : lacking special HW support

            ⇒ There is no real greybox IoT fuzzer with good throughput (~19)

        4. Motivation. Solve below issues

        1) Memory address translation

        2) Dyanmic code translation

        3) Syscall emulation

    4. Augmented Process Emulation

        1. Solution overviews:

        1) Combining user-mode emulation with system-mode emulation

        2) By using RAM file, Two modes share the memory state

        3) Process is migrated only if it calls Syscall, and come back after Syscall

        2. Memory Mapping : Whenever migration is occured, Memory info is sent to system-mode

        ⇒ use Efficient memory mapping

        3. Page fault handling : Page fault have to be handled by System-mode. The reason that Most page faults are occured is the page is not present.

        4. Preload page mapping ⇒ Prevent repeated page fulat

        5. System call redirection : System call cannot be handled by user-mode

        So, At the moment when system call, We pause the execution in the system-mode emulation, and Save the CPU states, and Pass it to System-mode.

        There are many Syscall that can be handled at user-mode such as Write, Read, etc

    5. Firm-AFL Design and Implementation

        1. Work flow based on AFL

        2. Forking at certain point where all settings are finished

        3. Collecting coverage informations : Just like the original AFL does it. because System-mode is only needed for handling page faults and some system calls.

    ### 해당 논문을 읽고.

    난 처음 이논문의 제목을 보고, 신박한 가상화 기법을 제안하는 논문인줄 알았으나 가상화는 전제이고 가상화를 이용할 때 더 빠르게 Greybox fuzzing을 수행하기 위한 방법을 제공한다.

    따라서 우리 프로젝트에 이를 이용하려면, Target device를 가상화하고, 이를 이용하여 Fuzzing을 진행하려고 할 때 더 효율적인 Fuzzing을 진행할 수 있겠다. 특히, github에 Firm-AFL 툴을 제공하고 있다.

    다행인 것은 가상화만 된다면, AFL을 효율적으로 수행할 수 있는 Firm-AFL이라는 툴이 존재한다는 것이다. (그러나, README가 너무 단순해서 걱정됨)

- [HALucinator](https://www.usenix.org/conference/usenixsecurity20/presentation/clements) 논문 읽기 및 생각

- Tracer 논문 읽기

  

# Conclusion of this weak

  

현재 우리가, 그리고 네달이라는 시간 동안 Orthogonal한 새로운 Architectur를 생각하기는 어려울 것같다.

  

따라서, 가상화를 하여 Fuzzing을 수행했던 지난 연구(Firm-AFL, HALucinator)들의 방법론을 참고하여 Fuzzing을 수행하는 것을 목표로 삼아야 겠다는 생각을 했다.

  

특히 가장 큰 문제라고 생각하는 가상화의 경우 HAL을 이용하여 handler를 필요에 따라 등록하는 HALucinator를 활용하여야 겠다.

따라서 다음 주 목표는 HALucinator에서 사용된 펌웨어를 이용하여 직접 재현 실험을 해보거나, 간단한 IoT 펌웨어로 Firm-AFL에 도전해야 겠다.

  

다만, Firm-AFL의 경우 직접 가상화를 해야함으로 HALucinator를 우선시 해야겠다.