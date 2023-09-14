232023-09-15 05:12
#### Topics: #zettelkasten #BoB #bobup #Project
---
## Workspace
### Index
1. QEMU, KVM Overview
	1. Hypervisor
	2. Emulation vs Simulation
	3. Full-Virtualization vs Para-Virtualization
	4. QEMU vs KVM
2. I/O Emulation
	1. I/O Full-virtualization
	2. I/O Para-virtualization
	3. vhost

### Hypervisor
* QEMU and KVM are some kinds of Hypervisor.
* Hypervisor : Software that schedules multiple OSs on the single machine
* Two types of Hypervisor
	  ![[Pasted image 20230915051930.png]]
	1. Type1 without OS
		ex) XEN, VMware's ESXi
	2. Type2 : Hypervisor on the OS. aks Hosted type
		ex) KVM, QEMU, Virtualbox
	![[Pasted image 20230915052216.png]]
### Emulation VS Simulation
* diff between Emulation and Simulation makes diff between QEMU, KVM
#### Emulation
에뮬레이션은 호스트 머신에 존재하지 않는 하드웨어 및 아키텍처를 가상머신에 제공
ex) X86 머신에서 동작하는 ARM 기반의 Android OS

QEMU가 대표적인 emulator that implements variety HWs

QEMU는 가상머신 위에서 생성된 ARM 기반의 명령어를 x86 기반의 명령어로 번역
이것을 Binary Translation이라고 함.
또한 하드웨어 장치 또한 소프트웨어로 구현
=> Slow

#### Simulation
시뮬레이션은 호스트 머신에 존재하는 하드웨어 및 아키텍처를 이용 가상머신에게 제공하는 것.
즉, Binary Translation을 수행하지 않고, 호스트 머신에 존재하는 하드웨어를 기반으로 가상 머신 환경을 꾸밀 수 있음
따라서, 가상머신에서 생성된 명령어를 번역할 필요없이 호스트 머신에서 직접 처리할 수 있음
=> Faster
보통 QEMU로 가상머신을 생성할 때, 호스트 머신과 가상 머신이 같은 종류의 CPU라면, KVM 옵션을 enable함 => -enable-kvm
![[Pasted image 20230915054934.png]]

### Full Virtualization VS Para Virtualization
#### Full Virtualization
전 가상화는 가상 머신에서 구동될 OS 혹은 Device Driver(DD)에 특별한 수정이 필요없는 경우.
즉, 물리 환경에서 동작하는 운영체제와 장치의 드라이버들을 그대로 가상 머신에서 사용할 수 있음
반 가상화에 비해 상대적으로 속도가 느림

#### Para Virtualization
수정된 Guest OS 혹은 수정된 DD를 사용하는 경우
대표적으로 Xen의 경우 최적화를 위해 Hyper Call을 활용하여 반 가상화

### QEMU vs KVM
#### QEMU
Emulation이 가능한 Hypervisor
호스트 머신과 독립적으로 가상화된 하드웨어 및 아키텍처를 동작시킬 수 있음
성능이 떨어짐
반 가상화 인터페이스 제공

#### KVM
같은 종류의 CPU 아키텍처만 가상화할 수 있는 Hypervisor
가상 머신이 생성한 명령을 호스트 머신이 직접 처리
성능 향상
반 가상화 인터페이스 제공

QEMU와 KVM이 함께 사용될 수 있음
호스트 머신과 게스트 머신이 동일한 CPU 아키텍처를 갖는 다면, KVM으로 가속화
DD는 QEMU의 도움을 받아 전가상화할 수 있음
![[Pasted image 20230915061059.png]]

---
### I/O 가상화
#### I/O Full-virtualization
I/O에서 사용되는 DD를 가상 머신용으로 특별히 수정하지 않고, Guest OS에서 사용하는 경우
DD에 대한 emulation은 QEMU가 수행
![[Pasted image 20230915061507.png]]
커널 영역에서 동작하는 KVM Module과 User 영역에서 동작하는 QEMU Emulator 사이의
Mode Transition Overhead로 인해, 느림

#### I/O Para-virtualization
반 가상화 I/O 인터페이스 VirtIO 사용
VirtIO의 Frontend는 가상머신 내의 커널에서 구동
호스트머신의 QEMU와 공유하는 메모리 영역인 Virtqeue를 통해 Device Backend와 연결됨
KVM Module을 거치지 않으므로, User Space에서만 동작해서 빠른듯

![[Pasted image 20230915061855.png]]
ex) virio-net, virio-blk, virio-scsi

#### vhost
VirtIO backend가 커널에서 직접 수행됨 ???????????????????
![[Pasted image 20230915062256.png]]
ex) vhost-net, vhost-scsi, vhost-blk 등


---
## Footer
#### Thinking
> 

#### Refs
* [# [네이버클라우드 기술&경험] 가상화 개념 이해하기#1, QEMU vs KVM](https://medium.com/naver-cloud-platform/%EB%84%A4%EC%9D%B4%EB%B2%84%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EA%B8%B0%EC%88%A0-%EA%B2%BD%ED%97%98-%EA%B0%80%EC%83%81%ED%99%94-%EA%B0%9C%EB%85%90-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-1-qemu-vs-kvm-962113641799)

#### Connected Notes
- [[Emulation]] 
- [[QEMU]]
- [[KVM]]
- [[Hypervisor]]
- [[Simulation]]
- [[Full-Virtualization]]
- [[Para-Virtualization]]
- [[I/O-Virtualization]]
- [[vhost]]
