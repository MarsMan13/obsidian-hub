232023-09-15 03:28
#### 주제: #zettelkasten #BoB #bobup #Project
---
## 내용
### Using Renode
#### Running and quitting Renode
* To start Renode, use the `renode` command, then pop up new window that is the Monitor
* `quit` command
#### Monitor
The monitor is used to interact with Renode and control the emulation

####  Basic interactive workflow
Start by creating the emulation through a sequence of commands building up, configuring, and connecting the relevant emulated platform(s)(aka machines)

This is done using nested **.resc script** to enchapsulate some of the repeatable commands?

When the emulation is created and all the necessary elements are loaded, Start with `start` command in the Monitor

As running,
We can use logger window
interact with the external interfaces of the emulated machines like UARTs or Ethernet controllers

#### .resc scripts
Renode scripts(.resc) enable you to encapsulate repeatable elements
Renode has many built-in `.resc` files
So we can use it by `include` command




---
#### 생각
> 

#### 출처
* 

#### 연결문서
- [[Renode]]
