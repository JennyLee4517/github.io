---
title: "[운영체제] 8. Memory Management"
date: 2019-10-10 21:30:00 -0400
categories: CS
comments: true

---

## Logical vs Physical Address ##
* Logical address(=virtual address)
	* 프로세스마다 독립적으로 가지는 주소공간
	* 각 프로세스마다 0번지부터 시작
	* CPU가 보는 주소가 이 주소  

* Physical address
	* 메모리에 실제 올라가는 위치  

* 주소 바인딩(주소 변환)
	* 주소를 결정하는것
	* Symbolic Address(사용자가 만든) -> Logical Address(프로세스가 갖는) -> Physical address(실제 주소)
	* Logical -> Physical로 바꿔주는 시점이 언제일까?

## 주소 변환 ##
* Compile time binding
	* 물리적 메모리주소가 컴파일 시 알려짐
	* 시작 위치 변경시 재컴파일
	* 컴파일러는 절대코드(absolute code) 생성 - 바꾸고 싶으면 컴파일을 다시 해야함

* Load time binding
	* Loader의 책임 하에 물리적 메모리 주소 부여(비어있는 곳에 올림)
	* 컴파일러가 재배치 가능코드(relocatable code)를 생성한 경우 가능함
	
* Execution time binding(=Run time binding)
	* 수행이 시작된 이후에도 프로세스 메모리 상 위치를 옮길 수 있음
	* CPU가 주소를 참조할 때마다 binding을 점검 (address mapping table)
	* 하드웨어적인 지원이 필요(e.g., base and limite registers, MMU)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_01.png?raw=true) 	


## Memory-Management Unit (MMU) ##
* MMU
	* logical address를 physical address로 매핑해주는 하드웨어 장치
* MMU scheme
	* 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소 값에 대해 base register (=relocation register)의 값을 더한다
	* 두개의 레지스터로 변환함(relocation register & limit register)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_02.png?raw=true) 
	
	* limit register는 프로세스 주소공간의 최대 크기를 이용해 이 프로세스가 악의적으로 남의 주소공간을 접근할 수 없도록 제한한다(trap : addressing error)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_03.png?raw=true) 	


* User program
	* logical address 만을 다룬다
	* 실제 physical address를 볼 수 없으며 알 필요가 없다
	
## Dynamic Loading ##
* 로딩 : 메모리로 올리는것
* 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 로드 하는것
* memory utilization의 향상
* 가끔씩 사용되는 많은 양의 코드의 경우 유용함(예 : 오류 처리 루틴 - 사용량은 적지만 코드 양은 많음)
* 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능함(OS는 라이브러리를 통해 지원 가능)
* 운영체제의 페이징이랑 말과 혼동되기도 한다(원래 다르긴 함)

## Overlays ##
* 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
* 프로세스의 크기가 메모리보다 클 때 유용
* 운영체제의 지원 없이 사용자에 의해 구현
* 작은 공간의 메모리를 사용하던 초창기 시스템에서 수작업으로 프로그래머가 구현
	* Manual Overlay
	* 프로그래밍이 매우 복잡 
* 다이나믹 로딩과의 차이점 : 쟤는 메모리가 딸려서 그런게 아니고 utilization높이려고 하는거고, 이거는 메모리가 딸려서 그런거임(초창기) 그리고 프로그래머가 직접 짜는것!

## Swapping ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_04.png?raw=true) 	

* Swapping
	* 프로세스를 일시적으로 메모리에서 backing stroe(하드디스크)로 쫓아 내는것
* Backing store(=swap area)
	* 많은 사용자의 프로세스 이미지를 담을만큼 충분히 빠르고 큰 저장 공간
	
* Swap in / Swap out
	* 일반적으로 중기 스케줄러(swapper)에 의해 swap out 시킬 프로세스를 선정함
	* priority-based CPU scheduling algorithm : 낮으면 out, 높으면 in
	* Compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in 해야 함 - 좀 비효율적임 
	* Execution time binding(런타임 바인딩)에서는 추후 빈 메모리 영역 아무곳에나 올릴 수 있음 - 이게 좀 더 효율적
	* Swap time은 대부분 transfer time(swap되는 양에 비례하는 시간)과 같음

## Dynamic Linking ##
* Linking을 실행시간까지 미루는 기법
* Static linking
	* 라이브러리가 프로그램의 실행 파일 코드에 포함됨
	* 실행파일의 크기가 커짐
	* 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리가 낭비됨(eg. printf 함수의 라이브러리 코드)
	
* Dynamic linking
	* 라이브러리가 실행시 연결됨
	* 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기위한 stub이라는 작은 코드를 둠
	* 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
	* 운영체제의 도움이 필요
	* Shared library 라고 부른다 
	
	
## Allocation of Physical Memory ##
* 메모리는 일반적으로 두 영역으로 나뉘어 사용  
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_05.png?raw=true) 	

	* OS 상주 영역 : Interrupt vector와 함께 낮은 주소 영역 사용
	* 사용자 프로세스 영역 : 높은 주소 영역 사용

* 사용자 프로세스 영역의 할당 방법
	* Contiguous allocation(연속할당) : 각각의 프로세스가 메모리의 연속적인 공간에 통째로 적재되도록 하는것
		* Fixed partition allocation
		* Variable partition allocation
	* Noncontiguous allocation(불연속할당) : 하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음 - 현대
		* Paging : 같은 크기로 잘라서 올리기
		* Segmentation : 의미 단위로 잘라서 올리기(코드, 데이타, 스택 -> 물론 더 잘게 쪼개는것도 가능), 크기가 일정치 않기 때문에 당연히 조각이 발생됨
		* Paged Segmentation
		
## Contiguous Allocation ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_06.png?raw=true) 	

* 고정분할(Fixed partition) 방식
	* 물리적 메모리를 몇개의 영구적 분할(파티션)으로 나눔
	* 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 있다
	* 분할당 하나의 프로그램 적재
	* 딱봐도 융통성 없어보임;;
	* 동시에 메모리에 로드되는 프로그램의 '수'가 고정됨
	* 동시에 최대 수행 가능한 프로그램의 '크기'또한 제한됨
	* internal fragmentation발생(external fragmentation도 발생)
	* 내부조각 : 프로그램 올리고 남는 공간
	* 외부조각 : 조각의 크기가 작아 프로그램을 올릴 수 없어서 남는 공간
* 가변분할(Variable partition) 방식 
	* 프로그램의 크기를 고려해서 할당
	* 분할의 크기, 개수가 동적으로 변함
	* 기술적 관리 기법이 필요
	* External fragmentation 발생 (실행이 끝났다고 해서 그 공간이 채워지도록 땅겨 올라가는것이 아니기 때문)

* Hole
	* 가용 메모리 공간
	* 다양한 크기의 홀들이 메모리 여러곳에 흩어져 있음
	* 프로세스가 도착하면 수용 가능한 홀을 할당
	* 운영체제는 다음의 정보를 유지 : 할당공간, 가용공간(hole)
	![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_07.png?raw=true) 	

	
* Dynamic Storage-Allocation Problem
	* 가변 분할 방식에서 size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제
	* First-fit
		* 사이즈가 n 이상인 것 중 최초로 찾아지는 홀에 할당하기
	* Best-fit
		* Size가 n이상인 가장 작은 hole을 찾아서 할당
		* 홀들의 리스트가 크기순으로 정렬되지 않은 경우 모든 리스트를 탐색해야함
		* 많은 수의 아주 작은 hole들이 생성됨
	* Worst-fit
		* 가장 큰 hole에 할당하기
		* 역시 모든 리스트를 탐색해야함
		* 상대적으로 아주 큰 hole들이 생성됨
		
	* first-fit, best-fit이 worst-fit보다 속도와 공간 이용률 측면에서 효과적인 것으로 알려짐(실험적 결과)
	
* Compaction
	* external fragmentation 문제를 해결하는 한가지 방법
	* 사용중인 메모리 영역을 한군데로 몰고, 홀들을 다른 한곳으로 몰아 큰 블럭을 만드는것
	* 매우 비용이 많이 든다
	* 최소한의 메모리 이동으로 compaction하는 방법(매우 복잡한 문제)
	* 컴팩션은 프로세스의 주소가 실행시간에 동적으로 재배치 가능한 경우에만 수행될 수 있다
	

## Paging ##
* 페이징
	* 프로세스의 가상메모리를 동일한 사이즈의 페이지 단위로 나눔
	* 가상메모리의 내용이 페이지 단위로 noncontiguous하게 저장
	* 일부는 backing storage에, 일부는 physical memory에 저장
* Basic Method
	* physical memory를 동일한 크기의 프레임으로 나눔
	* logicalmemory를 동일 크기의 페이지로 나눔(프레임과 같은 크기겠죠 당연히?)
	* 모든 가용 프레임들을 관리
	* 페이지 테이블을 사용하여 logical address를 physical address로 변환
	* 외부조각은 발생하지 않지만, 내부프레임은 발생 가능(꼬다리)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_08.png?raw=true) 	
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_09.png?raw=true) 	


## Implementation of Page Table ##
* Page table 은 main memory에 상주(크기가 커서 레지스터나 캐시에 보관하긴 무리임)
* Page-table base register(PTBR) 가 page table을 가리킴
* Page-table length register(PTLR) 가 테이블 크기를 보관
* 모든 메모리 접근 연산에는 2번의 메모리 접근이 필요
* 페이지 테이블 접근 한번, 실제 데이타/명령어 접근 1번
* 속도 향상을 위해 associative register 혹은 translation look-aside buffer(TLB)라 불리는 고속의 lookup hardware cache사용 - 주소변환을 위한 캐시메모리(페이지 테이블 전체를 갖고 있는건 아니고 자주쓰는 일부만)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_10.png?raw=true) 	


* TLB는 전부 가지고 있는것이 아니기 때문에 p와 f를 쌍으로 가지고 있으며, 전체검색으로 다 찾아봐야함 (페이지 테이블은 위치 자체에 f가 딱 들어가서 전체검색 할 필요가 없음) - Parallel search
* 페이지 테이블은 프로세스마다 각각 존재함. TLB도 마찬가지
* Address Translation
	* 페이지테이블 중 일부가 TLB 에 보관되어 있음
	* 만약 해당 페이지 넘버가 TLB에 있는 경우 곧바로 프레임 넘버를 얻음
	* 그렇지 않은 경우 메인메모리에 있는 페이지 테이블로부터 프레임 넘버를 얻음
	* TLB는 문맥교환시 flush됩니다~(모든 엔트리 비우기)

## Effective Access Time ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_11.png?raw=true) 	

* hit : TLB접근(ε) -> 찾았군! -> 메모리접근(1) = 1 + ε
* miss : TLB접근(ε) -> 못찾음 -> 페이지테이블 접근(1) -> 메모리접근(1) = 2 + ε
* TLB없이 그냥 페이지 테이블만 있으면 기본 2 + ε
* 2+ε > 2+ε-α 이므로 TLB가 있는게 이득이다

## Two-Level Page Table ##
* 현대의 컴퓨터는 주소공간이 매우 큰 프로그램도 지원한다
	* 32비트 주소 사용시 2^32(4G)의 주소공간
		* 페이지 사이즈가 4K일때 백만개의 페이지 테이블 엔트리가 필요하게 됨
		* 각 페이지 엔트리가 4바이트면, 프로세스당 4백만의 페이지 테이블이 각 프로세스마다 필요하단 얘기
		* 그러나 대부분의 프로그램은 4기가의 주소공간 중 지극히 일부분만 사용하므로 페이지 테이블 공간 자체가 심하게 낭비되는 셈
	* 따라서 페이지 테이블 자체를 페이지로 구성!
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_12.png?raw=true) 	

	* 사용되지 않는 주소공간에 대한 outer page table의 엔트리값은 NULL (대응하는 inner page table이 없음) - 공간상의 이득을 취한다
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_13.png?raw=true) 	
	
	2^10 인 이유 = 1000개이므로
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_15.png?raw=true) 
	
	 
* 만약 64bit라면 ? 직접 생각해보자~



## Multilevel Paging and Performance ##
* 주소공간이 더 커지면 다단계로 페이지 테이블을 만들어야한다
* 각 단계의 페이지 테이블이 메모리에 존재하므로 논리주소의 물리주소 변환에 더 많은 메모리 접근이 필요하게 된다
* TLB를 통해 메모리 접근 시간을 줄일 수 있다
* 4단계 페이지 테이블을 사용하는 경우
	* 메모리 접근시간을 100ns, TLB접근시간이 20ns이고 TLB hit ratio가 98% 라면?
	* effective memory access time = 0.98*120 + 0.02*520 = 128ns 즉 결과적으로 주소변환만을 위해 28ns(128-100)만 필요함 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_16.png?raw=true) 	

* valid-invalid bit : 사용되지 않는 주소영역에 대해서도 엔트리는 만들어져야 하기 때문에 6,7번페이지는 없지만 테이블에는 존재해야 한다. 대신 invalid로 표시

## Memory Protection ##
* 페이지 테이블의 각 엔트리마다 아래의 비트를 둔다

	* Protection bit
		* 페이지에 대한 접근 권한(read/write/read-only)
	* Valid-invalid bit
		* valid : 해당 주소의 프레임에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함(접근 허용)
		* invalid : 해당 주소의 프레임에 유효한 내용이 없음을 뜻함(접근 불허)
		* 유효한 내용이 없다는 것은 프로세스가 그 주소부분을 사용하지 않는 경우도 있고, 해당 페이지가 swap area에 있는 경우도 있다

## Inverted Page Table ##
* 페이지 테이블은 대체 왜 큰걸까?
	* 모든 프로세스별로 그 논리주소에 대응하는 모든 페이지에 대해 페이지 테이블 엔트리가 존재하기 때문
	* 대응하는 페이지가 메모리에 있든 아니든 간에 페이지 테이블에는 엔트리로 존재하기 때문
* Inverted Page Table
	* 페이지 프레임 하나당 페이지 테이블에 하나의 엔트리를 둔것(system-wide) - 프로세스별로 있는게 아님 - 물리적 메모리의 프레임 갯수만큼 엔트리가 존재
	* 각 페이지테이블 엔트리는 각각의 물리적 메모리의 페이지 프레임이 담고있는 내용을 표시함(프로세스 id, 프로세스의 논리주소) 논리주소는 프로세스마다 겹칠 수 있으므로 테이지테이블에 프로세스 아이디도 같이 들어가야함
	* 물리주소로 논리주소를 알아낼 수 있는 구조
	* 단점 : 테이블 전체를 탐색해야함. 공간은 줄여주지만 시간에 대한 오버헤드가 있다.
	* 조치 : TLB 쓰쟈~ 공짜는 아니지만~
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_17.png?raw=true) 	
  

## Shared Page ##
* Shared code
	* Re-entrant Code (=Pure code)
	* read-only 로 하여 프로세스 간에 하나의 코드만 메모리에 올림(eg, text editors, compilers, window systems)
	* Shared code 는 모든 프로세스의 logical address space에서 동일한 위치에 있어야 함 - 왜냐하면 코드안에 적혀있는 논리주소가 같다는걸 보장해야하기 때문
* Private code and data
	* 각 프로세스들은 독자적으로 메모리에 올림
	* private data는 논리주소공간의 아무곳에 와도 무방
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_18.png?raw=true) 	

## Segmentation ##
* 프로그램은 의미 단위인 여러개의 segment로 구성
	* 작게는 프로그램을 구성하는 함수 하나하나를 세그먼트로 정의
	* 크게는 프로그램 전체를 하나의 세그먼트로 정의 가능
	* 일반적으로는 코드,데이타,스택 부분이 하나씩의 세그먼트로 정의됨
* 세그먼트는 다음과 같은 논리적 단위들이다
	* main함수 / 사용자함수 / 전역변수 / 스택 / 심볼테이블 / 배열들
* 논리 주소는 다음의 두가지로 구성 : segment-number, offset
* 세그먼트 테이블
	* 각각의 테이블 엔트리는 다음으로 구성
		* base = starting physical address of the segment
		* limit = length of the segment (d > limit 이면 트랩 발생)
* Segment-table base register(STBR)
	* 물리적 메모리에서의 세그먼트 테이블의 위치
* Segment-table length register(STLR)
	* 프로그램이 사용하는 세그먼트의 수(몇개의 세그먼트로 되어있는지)
	* segment number s is legal if s < STLR
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_19.png?raw=true) 	
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_20.png?raw=true) 	


페이지는 갯수가 굉장히 많지만 세그먼트는 갯수가 크지 않기때문에 테이블을 위한 메모리낭비가 비교적 적다

## Segmentation Architecture(Cont.) ##
* Protection
	* 각 세그먼트별로 protection bit가 있음
	* 각각의 엔트리별로 valid bit, read/write/execution 권한 bit 이 있음
* Sharing
	* 공유 세그먼트
	* same segment number
	* 세그먼트는 의미 단위로 쪼개진 것이므로 공유와 보안에 있어 페이징보다 훨씬 효과적이다
* Allocation
	* first fit/ best fit
	* 외부조각 발생 : 세그먼트의 길이가 동일하지 않으므로 가변분할 방식에서처럼 동일한 문제점들이 발생한다 
	
	
## Paged Segmentation ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/08_21.png?raw=true) 	

* 세그먼트를 여러가지 페이지로 구성하는 기법
* 외부조각이 생기는 문제를 해결
* 세그먼트 당 페이지 테이블이 존재
* 세그먼트에 대한 주소변환을 먼저함 -> 해당 페이지 테이블로 감 



------

본 포스팅은 [이화여대 반효경 교수님의 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323&ar=pop)를 기반으로 만들어졌습니다.  

문제시 삭제하도록 하겠습니다.  
