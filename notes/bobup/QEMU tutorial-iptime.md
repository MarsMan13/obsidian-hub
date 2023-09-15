232023-09-15 04:09
#### Topics: #zettelkasten #BoB #bobup #Project
---
## Workspace
### Setup env
1. download iptime firmware
	`wget http://download.iptime.co.kr/online_upgrade/t24000_kr_9_988.bin`
2. Binwalk 설치
	`sudo apt-get install binwalk`

### Extract iptime-firmware
1. Recognizing Firmware Architecture : `binwalk -A t240000_kr_9_988.bin`
	![[Pasted image 20230915041511.png]]
	=> ARM architecture
2. Recognizing Signature : `binwalk -B t24000_kr_9_988.bin`
	![[Pasted image 20230915041840.png]]
		1) header at 0x400 : OS-Linux, CPU-ARM, Entry Point: 0x8000
		2) Kernel image at 0x440
		3) File System at 0x1A0000
3. Extracting Firmware : `binwalk -e t24000_kr_9_988.bin`
4. `wget https://people.debian.org/~aurel32/qemu/armel/vmlinuz-3.2.0-4-versatile 
   `$ wget https://people.debian.org/~aurel32/qemu/armel/initrd.img-3.2.0-4-versatile`
   `$ wget https://people.debian.org/~aurel32/qemu/armel/debian_wheezy_armel_standard.qcow2`
`
5. QEMU 실행 : 
	```
	qemu-system-arm -M versatilepb -kernel vmlinuz-3.2.0-4-versatile -initrd initrd.img-3.2.0-4-versatile -hda debian_wheezy_armel_standard.qcow2 -append "root=/dev/sda1" -net user,hostfwd=tcp::2080-:80,hostfwd=tcp::2022-:22
	```
	qemu-system-arm : ARM 아키텍처
	-M versatile : 사용할 머신의 타입 (ARM 보드)
	-kernel vmlinuz-3.2.0-4-versatile : 리눅스 커널
	-initrd initrd.img-3.2.0-4-versatile : 초기 RAM 디스크 이미지
	-hda debian_wheezy_ : 사용할 하드 디스크 이미지 (.qcow2 : QEMU에서 사용하는 이미지 형식)
	-append "root=/dev/sda1" : 커널에 전달될 부팅 옵션 => /dev/sda1으로 사용
	-net : 포트 리다이렉션 between host and guest
	![[Pasted image 20230916041225.png]]
 6. Place filesystem at Guest by using scp. And unpack it
    `sudo scp -P 2022 squashfs-root.tar root@localhost:/root`
	`tar xvf squashfs-root.tar`
7. CHROOT
8. cgibin 파일 복사
9. `./sbin/httpd`
---
## Footer
#### Thinking
> 위의 과정을 살펴보면 사실
> 1. binwalk를 통해 펌웨어의 아키텍처와 파일시스템을 추출하고
> 2. 해당 펌웨어에 알맞게 가상화를 한 후
> 3. 가상화된 OS에 파일시스템을 옳기는 과정이다


#### Refs
* [[ 정보보안 프로젝트 ] 공유기 펌웨어 환경 구축](https://devdori.tistory.com/45)
* [IoT-장비-분석-공유기-펌웨어-분석](https://laoching.tistory.com/entry/IoT-%EC%9E%A5%EB%B9%84-%EB%B6%84%EC%84%9D-%EA%B3%B5%EC%9C%A0%EA%B8%B0-%ED%8E%8C%EC%9B%A8%EC%96%B4-%EB%B6%84%EC%84%9D)
 * [해커스쿨 강의자료](./공유기해킹-ARMexploitation.pdf)

#### Connected Notes
* [[QEMU tutorial]]
* [[binwalk]] 
* [[iptime]]