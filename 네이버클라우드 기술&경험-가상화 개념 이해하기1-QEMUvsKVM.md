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

### 1. Hypervisor
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


---
## Footer
#### Thinking
> 

#### Refs
* [# [네이버클라우드 기술&경험] 가상화 개념 이해하기#1, QEMU vs KVM](https://medium.com/naver-cloud-platform/%EB%84%A4%EC%9D%B4%EB%B2%84%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EA%B8%B0%EC%88%A0-%EA%B2%BD%ED%97%98-%EA%B0%80%EC%83%81%ED%99%94-%EA%B0%9C%EB%85%90-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-1-qemu-vs-kvm-962113641799)

#### Connected Notes
- [[emulator]] 