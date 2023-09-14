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
2. 


---
## Footer
#### Thinking
> 

#### Refs
* [[QEMU tutorial]]
* [[binwalk]] 
* [[iptime]]

#### Connected Notes
- 