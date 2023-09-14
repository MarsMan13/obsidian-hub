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
	

---
## Footer
#### Thinking
> 

#### Refs
* [[ 정보보안 프로젝트 ] 공유기 펌웨어 환경 구축](https://devdori.tistory.com/45)
* [IoT-장비-분석-공유기-펌웨어-분석](https://laoching.tistory.com/entry/IoT-%EC%9E%A5%EB%B9%84-%EB%B6%84%EC%84%9D-%EA%B3%B5%EC%9C%A0%EA%B8%B0-%ED%8E%8C%EC%9B%A8%EC%96%B4-%EB%B6%84%EC%84%9D)


#### Connected Notes
* [[QEMU tutorial]]
* [[binwalk]] 
* [[iptime]]