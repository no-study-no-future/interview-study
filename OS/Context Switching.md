# Context Switching
## 요약
- Interrupt이란 예상치 못하거나, 외부에서 발생한 이벤트, CPU 사용시간 만료 등으로 인해 프로세스를 잠시 중단하는 것
- Context Switching란 Interrupt 발생 시 현재 프로세스의 상태를 저장하고, 새 프로세스의 저장된 상태를 다시 적재하는 작업
- Context Switching시 시간과 비용이 발생하는데 이를 Overhead라 하며, 이를 줄이기 위해 Thread를 사용
- Multi-Process는 overhead 비용이 발생하며, Multi-Thread는 overhead는 줄였지만 동기화 문제를 주의해야함

## Context Switching
### Context란
- CPU가 해당 프로세스를 실행하기 위한 프로세스의 정보를 말함
- 운영체제는 프로세스를 제어하기 위해 PCB(Process Control Block)에 프로세스 상태 정보를 저장함

### Scheduling이란
- 자원에 작업을 할당하는 행위
- 자원은 Processor, 네트워크 연결, 외부 장치 등을 의미하며, 작업은 thread, process 혹은 data flows를 의미
- scheduling은 scheduler라는 프로세스에 의해 수행되며, 모든 자원이 골고루 사용될 수 있게 디자인됨
> 프로세서(Processor) : CPU 등 하드웨어 측면  
> 프로그램(Program) : 실행 파일  
> 프로세스(Process) : 실행 중인 프로그램  

### Context Switching이 필요한 이유
- CPU의 코어가 1개(자원이 1개)라면 동시에 단 하나의 프로세스만 실행 가능
- 반응속도가 매우 느리고 사용하기 불편
- 따라서 CPU Scheduling을 통해 하나의 CPU를 여러 작업들이 공유할 수 있게 CPU 시간을 나누어 작업
- 이때 프로세서가 지금까지 실행되던 프로세스를 중지하고(Interrupt) 다른 프로세스의 PCB 정보를 바탕으로 Thread를 실행하는 것을 Context Switching이라고 함

### Interrupt
- 예상치 못하거나, 외부에서 발생한 이벤트로 인해 프로세스를 잠시 중단하는 것
- 컴퓨터 시스템에서 발생하는 예외적인 사건을 신속히 처리하기 위한 기법
- 종류
	- 입출력 요청
	- CPU 사용시간 만료
	- 자식 프로세스 만들때
	- 인터럽트 처리를 기다릴 때
	- 생략

### Context Switching
- 현재 진행하고 있는 Task(Process, Thread)의 상태를 저장하고 다음 진행할 Task의 상태 값을 읽어 적용하는 과정
- 진행과정
	- Task 정보는 Register에 저장되고 PCB로 관리
	- 현재 실행하고 있는 Task의 PCB 정보를 저장
	- 다음 실행할 Task의 PCB 정보를 읽어 Register에 적재, 이전에 진행했던 과정을 연속적으로 수행

### PCB(Process Control Block)
- Process State : 프로세스 상태(Create, Ready, Running, Waiting, Terminated)
- **PC**(Process Counter) : 다음 실행할 명령어의 주소값
- **SP**(Stack Pointer) : 스택에 데이터가 채워진 마지막 위치를 가리키는 레지스터
- CPU Registers : accumulator, index register, stack pointers, general purpose registers
> PC, SP 중요  
![](Context%20Switching/1.png)
![PCB 구조](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/OS/PCB%E1%84%80%E1%85%AE%E1%84%8C%E1%85%A9.png?raw=true) 

## 문제점
### Cost 비용
- Cache 초기화
- Memory Mapping 초기화
- Kernel은 항상 실행(메모리 접근을 위해)

### Overhead
- 사용된 시간과 사용된 메모리의 양을 의미
- Context Switching이 잦게 발생할수록 Overhead 비용이 발생하여 성능이 떨어짐
- P0에서 P1으로 Context Switching할 시 P1의 상태를 PCB에서 가져오는데 시간이 소요됨(P1의 유휴 상태(idle))
![Context Switching](https://github.com/leeejuhyeong/images/blob/main/no-study-no-future/OS/ContextSwitching.jpg?raw=true)  

### 해결방안
- Context Switching은 자주 발생하기 때문에 가능한 효율적으로 구현해야하며 또한 최소한의 자료들을 주기억 장소로 옮겨야 함
- 이를 위해 Thread를 이용

### Thread Context Switching
- 프로그램이 단일 프로세스로 생성되면 하나의 실행점(Excution)만이 존재함으로 병행 처리가 불가능
- 프로그램이 여러 프로세스로 생성되면 각 프로세스마다 동일한 많은 정보들이 중첩 유지되어 관리되기에 비효율적
- 단일 프로세스를 다수의 Thread로 생성하여 병행성을 증진, 실행환경을 공유
- 최종적으로 Context Switching 시 메모리 전환이 없기에 오버헤드를 줄임

## 정리
### Multi-Process 장점
- 독립된 구조로 안전성 높음
- 프로세스 중 하나에 무제가 생겨도 다른 프로세스에 영향X

### Multi-Process 단점 : Multi-Process Context Switching
- context와 함께 메모리 전환도 이루어짐
- Register 메모리 전환 이루어짐
- Process 메모리 할당 필요
- TLB(Transtation Lookaside Buffer) 플러싱 발생
	- TLB는 가상 메모리 공간 할당 속도를 높여줌(물리, 가상 매핑 캐시 역할)
	- TLB가 일어나 다시 메모리를 읽고 매핑해야 함
- 캐싱은 메모리를 유지하여 지역성 효율을 높이는데 의미가 있지만 메모리를 재생성하기 때문에 효율이 급감
- 결론 - context, memory, 주소 공간 모두 전환

### Multi-Thread 장점
- 시스템 자원소모 감소
- 시스템 처리율 향상(처리비용 감소 -> Thread 간 데이터 공유)
- 프로그램 응답시간 단축(Thread간 힙 영역 공유로 데이터 통신)

### Multi-Thread Context Switching
- context 전환만 이루어짐
- 메모리 전환이 없기에 할당 X
- 캐싱 효율 좋음
- 공유 메모리라면 메모리 접근 시 지역성 효율이 높음
- 커널 스레드 간 교환, 종료 후엔 사용자 스레드 전환

### Multi-Thread 단점
- 동기화 문제 발생(병목현상, 데드락 등)
- 주의 깊은 설계 필요
- 하나의 Thread에 문제가 생기면 전체 프로세스가 영향을 받음)

### 비교
- Context Switching 비용은 Process가 많이 듬
- Thread는 Stack 영역을 제외한 모든 메모리를 공유하기 때문에 Context Switching 발생 시 Stack 영역만 변경 진행
- 반면에 Process는 thread의 context뿐만 아니라 processor의 context까지 모두 변경해야함

### 재미 요소
- Internet Explore
	- 멀티 스레드
	- 메모리 사용 낮음
	- 여러 화면 중 하나에 문제가 생기면 익스플로어 전체 종료
- Chrome
	- 멀티프로세스
	- 메모리 사용 높음
	- 하나의 탭이 문제가 되도 다른 화면에는 미치지 않음



## 출처
- [콘텍스트 스위칭(Context Switching) :: 으뜸별](https://beststar-1.tistory.com/26)
- [OS - Context Switch(컨텍스트 스위치)가 무엇인가?](https://jeong-pro.tistory.com/93)
- [Context Switching 이란?](https://velog.io/@curiosity806/Context-Switching%EC%9C%BC%EB%A1%9C-%EC%95%8C%EC%95%84%EB%B3%B4%EB%8A%94-process%EC%99%80-thread)
- [2. CPU와 프로세스 · What is OS System?](https://jihooyim1.gitbooks.io/iknowosbasic/content/contents/02.html)
- [Context Switching와 Interrupt](https://velog.io/@hayeon/Context-Switching%EC%99%80-Interrupt)
- [OS기초 인터럽트 제대로 이해하기](https://velog.io/@adam2/%EC%9D%B8%ED%84%B0%EB%9F%BD%ED%8A%B8)
- [OS 멀티 프로세스와 멀티 스레드 관점에서 본 Context Switching](https://velog.io/@jaeyunn_15/OS-%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EB%A9%80%ED%8B%B0-%EC%8A%A4%EB%A0%88%EB%93%9C-%EA%B4%80%EC%A0%90%EC%97%90%EC%84%9C-%EB%B3%B8-Context-Switching)
- [OScontext switching](https://junghyungil.tistory.com/105)
- [멀티 프로세스(Multi Process)와 멀티 스레드(Multi Thread) - wooody92’s blog](https://wooody92.github.io/os/%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4%EC%99%80-%EB%A9%80%ED%8B%B0-%EC%8A%A4%EB%A0%88%EB%93%9C/)