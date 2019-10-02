---
title: "[운영체제] 2. System Structure & Program Execution"
date: 2019-10-02 20:50:00 -0400
categories: CS
comments: true

---

* 컴퓨터 시스템 구조
![02_01](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/02_01.png?raw=true)
* 메모리 : CPU 의 작업 공간 - CPU가 매 클럭마다 여기서 명령어를 읽어들임
	* 사용자 프로그램 : I/O 작업을 직접 I/O Controller에게 명령할 수 없게 막혀있음. 따라서 운영체제에게 CPU더러 대신 I/O Controller에 명령을 내려달라고 부탁하며 제어권을 반납하게 됨.
* CPU
	* 레지스터
	* 모드비트 : 사용자 모드 - 1, 커널 모드 - 0
		* 보안의 목적
		* 커널 모드 : 모든 접근 가능(사용자 프로그램에게 CPU를 넘기기 전에 1로 바뀜)
		* 사용자 모드 : 제한된 접근 (인터럽트, 예외 발생시 0으로 바뀜)
	* 인터럽트 라인 : 인터럽트 들어온게 있는지 확인
		* 있다면 OS로 제어권을 넘김
* I/O device: device controller(작은 CPU), local buffer(작은 메모리), 레지스터
	* Device Controller는 CPU로부터 명령을 받고, 명령을 모두 수행하면 인터럽트를 건다
* 타이머
	* 특정 프로세스가 CPU를 독점하는걸 막기 위함
	* 타이머 인터럽트가 걸리면 CPU제어권이 사용자에서 커널로 넘어감
* DMA Controller
	* CPU처럼 메모리에 접근 가능
	* 메모리 컨트롤러에 의해 CPU와 동시에 접근은 못함
	* I/O장치에 의해 CPU가 너무 많이 방해받는걸 방지
	* I/O에서 받아온 데이타를 CPU 대신 메모리에 옮겨줌
	* 그 후에 CPU에게 인터럽트를 건다
* 입출력의 수행
	* 모든 입출력 명령은 특권 명령
	* 사용자 프로그램은 어떻게 I/O를 하는가?
		* 시스템 콜
			* 사용자 프로그램이 OS의 서비스를 받기 위해 거는 인터럽트
			* 사용자 프로그램은 메모리 주소 변경을 자기 프로그램 밖에는 못함 -> OS에 직접 요청x
			* 그래서 시스템 콜 사용자 프로그램이 직접 CPU에 인터럽트를 거는것을 말함
			* 그러면 제어권이 OS로 넘어감
			* 이러한 소프트웨어 인터럽트를 트랩이라 부른다
		* 트랩을 이용해 인터럽트 벡터의 특정 위치로 이동
			* 시스템 콜 : 프로그램이 커널 함수를 호출하는 경우
			* 예외 : 프로그램이 오류를 범한 경우
		* 제어권이 인터럽트 벡터가 가리키는 인터럽트 서비스 루틴으로 이동
			* 인터럽트 벡터 : 해당 인터럽트의 처리 루틴 주소를 갖고 있음
			* 인터럽트의 처리 루틴(인터럽트 핸들러) : 해당 인터럽트를 처리하는 커널 함수
		* 올바른 I/O요청인지 확인 후 수행
		* 완료 시 제어권을 시스템콜 다음 명령으로 옮김

* 동기식 입출력과 비동기식 입출력
![02_02](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/02_02.png?raw=true)
* 동기식 입출력
	* I/O 요청 후 입출력 작업이 완료된 후에야 제어가 사용자 프로그램에 넘어감
	* 구현법 1 
		* I/O가 끝날 때까지 CPU를 낭비시킴
		* 매 시점 하나의 I/O만 일어날 수 있음
	* 구현법 2
		* I/O가 완료될 때까지 해당 프로그램에게서 CPU를 빼앗음
		* I/O처리를 기다리는 줄에 그 프로그램을 줄세움
		* 다른 프로그램에게 CPU를 줌
* 비동기식 입출력
	* I/O가 시작된 후 입출력 작업이 끝나기를 기다리지 않고 제어가 사용자 프로그램에 즉시 넘어감
* 두 경우 모두 I/O의 완료는 인터럽트로 알려줌
 
* DMA ( Direct Memory Access ) 
	* 빠른 입출력 장치를 메모리에 가까운 속도로 처리하기 위해 사용
	* CPU의 중재 없이 device controller가 device buffer의 내용을 메모리에 block 단위로 직접 전송
	* 바이트 단위가 아닌 block 단위로 인터럽트를 발생시킴
	![02_03](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/02_03.png?raw=true)
* 서로 다른 입출력 명령어
	![02_04](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/02_04.png?raw=true)
* 저장 장치의 계층 구조
	![02_05](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/02_05.png?raw=true)
* 위로갈수록 비싸고 느리기 때문에 용량이 작음
* 바이트 단위로 접근이 되어야 Executable
* 캐싱 : 재사용 목적 
 
* 프로그램의 실행
![02_06](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/02_06.png?raw=true)
 
* 당장 필요한것만 Physical memory에 올리고 나머지는 Swap area에 둔다. 
* Swap area는 전원이 나가면 삭제됨 - 램의 연장공간으로 쓰는것 뿐임
* 주소 변환 : logical memory 주소 -> 실제 메모리 주소로 변환
![02_07](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/02_07.png?raw=true)
 
* 사용자 프로그램이 사용하는 함수
	* 사용자 정의 함수
	* 라이브러리 함수
	* 커널 함수 
		* OS 프로그램의 함수
		* 커널 함수를 호출하는것을 시스템 콜이라고 한다
 
![02_08](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/02_08.png?raw=true)

------

본 포스팅은 [이화여대 반효경 교수님의 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323&ar=pop)를 기반으로 만들어졌습니다.  

문제시 삭제하도록 하겠습니다.  
