# 프로세스 간 통신 방법(Inter Process Communication, IPC)
## 요약

## 프로세스 간 통신 (Interprocess Communication, IPC)
### 프로세스 간 통신?
- 프로세스는 완전히 독립된 실행객체 -> 다른 프로세스의 영향을 받지 않음
- 프로그램은 혼자 독자적으로 수행할 수도 있지만 프로그램들 사이에 정보를 교환함으로서 계산 속도를 증가시키거나 편의성을 향상시킬 수 있음
- 각 프로세스는 자신의 독립적인 메모리 공간을 가지고 다른 프로세스들에 의해 침범당하지 않도록 보호하고 있기 때문에 프로세스 간 통신을 위해서는 별도의 메커니즘이 필요

### 통신하려면?
- 프로세스가 통신이 가능하다? -> 서로 다른 프로세스가 데이터를 주고 받을 수 있다는 것
- 동시에 접근 가능한 메모리, **프로세스들이 공유하는 메모리가 필요**
- 커널 영역에서 IPC라는 내부 프로세스간 통신을 제공

### 커널(Kernel)?
- 운영체제 자체도 소프트웨어이기 때문에 메모리에 올라가야 사용할 수 있음
- 메모리를 효율적으로 사용하기 위해 **항상 필요한 부분만을 메모리에 올림**
- 메모리에 상주하는 운영체제 = 커널(Kernel)
	- 운영체제의 핵심적인 부분을 의미하기도 함

### 종류
- 공유 메모리 (Shared Memory)
- 메시지 전달 (Message Passing)
	- 메시지 큐 (Message Queue)
- 파이프 (Pipe)
	- 보통 파이프 (Ordinary Pipe)
	- 지명 파이프 (Named Pipe)
- 소켓 (Socket)
- 메모리 맵 (Memory Map)
- RPC (Remote Procedure Call)
	- 세마포어 (Semaphore)
	- 뮤텍스 

## 공유 메모리 (Shared Memory)
### 정의
- 프로세스들 사이에 공유되는 메모리 영역을 구축하는 방법
- 데이터 자체를 공유하도록 지원
- 커널이 관리하며, 프로세스가 공유 메모리 할당을 커널에 요청하면 커널은 해당 프로세스에 메모리 공간을 할당
	- 공유 메모리가 각 프로세스에게 Attach(첨부)하는 방식으로 작동
![공유 메모리](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/OS/shared_memory.png?raw=true)   
  
### 생성과 연결
- 프로세스 A
	- 공유 메모리 할당을 커널에 요청하면 메모리 공간 할당
	- 프로세스 Stack, Heap 중간 영역에 공유 메모리가 연결되게(Attach) 되어있음
- 프로세스 B
	- 자신의 주소 영역에 공유 메모리를 영역
	- A가 만든 공유 메모리를 어떤 Key로 만들었는지 알고 있음

### 특징
- 프로세스간 Read, Write를 모두 필요할 때 사용
- 대량의 정보를 다수의 프로세스에게 배포 가능
- 중개자 없이 곧바로 메모리에 접근 -> 모든 IPC 중 가장 빠르게 작동


## 메시지 전달 (Message Passing)
### 정의
- 커널 메모리 영역에 메시지 전달을 위한 채널을 생성
- 협려갛는 프로세스들 사이에 메시지 형태로 정보를 Send/Receive 하는 방법
- 메시지 큐(Message Queue)를 사용
![메시지 전달](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/OS/message_passing.png?raw=true)   
  

### 메시지 큐
- Queue를 사용해 메시지를 주고 받음 -> FIFO(First In First Out) 방식
- 먼저 들어온 메시지를 먼저 수신하지만, 메시지 큐의 msgtype에 따라 특정 메시지를 먼저 수신할 수도 있음
- EnQueue로 송신하고 deQueue로 수신
![](Inter%20Process%20Communication/E6FBE8B9-166D-41F8-BC6A-B9BD34AF7261.png)
![메시지 큐](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/OS/message_queue.png?raw=true)   

> Message Queue VS Message Passing  

## Shared Message VS Message Passing
### Shared Message
- 중계자가 없어 효율이 좋지만 다른 프로세스가 접근할 수 있도록 접근 금지를 풀어야 함
- 동시에 같은 메모리 위치를 접근하는 문제가 발생할 수 있음

### Message Passing
- 커널의 중계를 통해 메시지를 전달하며 큐를 사용해 비교적 안전
- Queue에 제정하고 꺼내는 과정에서 발생하는 Overhead로 인해 효율성 떨어짐 
![공유 메모리 vs 메시지 전달](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/OS/comparison.png?raw=true)   


## 파이프 (Pipe)
### 정의
- 메시지 전달(Message Passing)의 일종
- 통신을 위한 메모리 공간(버퍼)을 생성하여 프로세스가 데이터를 주고 받음





