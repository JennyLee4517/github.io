---
title: "[운영체제] 6. Process Synchronization(Concurrency Control)"
date: 2019-10-07 19:10:00 -0400
categories: CS
comments: true

---

### Process 데이터의 접근 패턴 ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_01.png?raw=true)
* 읽기만 한다면 누가 먼저 읽든 별 문제는 없다
* 근데 결과를 저장한다면 누가 먼저 읽어갔느냐 등에 따라 달라질 수 있고 그래서 동기화를 고려해야 한다
  
### Race Condition - 경쟁상태 ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_02.png?raw=true)
* 여러 주체가 하나의 데이터를 접근하려 하는것은 문제가 될 수 있음
* CPU가 하나면 괜찮지만, 여러 CPU를 쓰는 경우
* 혹은 프로세스 간 공유메모리를 사용할 때
* 하지만 더 중요한번 커널 내부 데이터를 접근하는 루틴들 간(예 : 커널모드 수행 중 인터럽트 커널모드 다른 루틴 수행시)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_03.png?raw=true)

### OS에서 race condition은 언제 발생하는가? ###
1. Interrupt handler vs kernel
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_04.png?raw=true)

* count 값이 +- 0 이길 기대하지만, 실제로는 증가된 값만 반영됨
* 그래서 이런 중요한 작업의 경우 작업 중간에 인터럽트 루틴으로 넘어가지 않고(disable) 작업이 끝나면 인터럽트 처리 루틴으로 넘어간다(enable)

### Preempt a process running in kernel ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_05.png?raw=true)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_06.png?raw=true)

* 카운트가 2 증가되길 기대되지만 실제로는 1만 증가되어버림
* 해결법은 단순.. 그냥 커널모드에 있을때는 할당시간이 끝났다고 해도 CPU를 선점당하지 않도록 해주면 된다.(잠시 유보) 커널모드가 끝나고 유저모드로 돌아올 때 CPU를 다른 프로세스에 주면 된다. 

### Multiprocessor ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_07.png?raw=true)
* 해결법1 : 데이타를 들어갈 때 데이터에 락을 걸어서 다른 CPU가 접근할 수 없도록 해야한다 
* 해결법2 : 커널을 하나의 CPU만 쓰도록 하는것이다. (비효율적)

  
### Process Synchronization 문제 ###
* 공유데이터의 동시접근은 데이터의 inconsistency를 발생시킬 수 있음
* 일관성 유지를 위해 협력 프로세스간 실행순서를 정해주는 매커니즘이 필요
* race condition을 막기 위해서는 concurrent process는 꼭 동기화 되어야한다.

### 임계구역 문제 ###
* n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우
* 각 프로세스의 code segment에 공유 데이터를 접근하는 코드인 critical section이 존재
* 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 그 구역에 들어갈 수 없어야 한다(기다리게 한다)

### 해결방법 ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_08.png?raw=true)
* 프로그램적 해결법 : 공유 섹션에 들어가기 전에 락을 걸고, 나오기 직전에 락을 푼다
* 충족해야 할 조건
	* Mutual Exclusion(상호배제) 
		* 프로세스가 임계구역 부분을 수행중이면 다른애들은 그 구역에 들어가면 안된다
	* Progress
		* 아무도 임계구역에 접근해있지 않은 상태에서 거기에 들어가고자 하는 프로세스가 있다면 들어갈 수 있도록 해줘야 한다
	* Bounded Waiting(유한대기)
		* 프로세스가 임계구역에 들어가려고 요청한 후 부터 그 요청이 허용될 때까지 다른 프로세스들이 임계구역에 들어가는 횟수에 한계가 있어야 한다
		* 예 ) 셋이 한 임계구역을 쓰려고 할때 둘이서만 계속 번갈아쓰면 나머지 하나는 starvation될 수 있다
	
### 알고리즘 1 ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_09.png?raw=true)
* P0는 turn이 0일때가 자기차례임 
* while(turn != 0); -> 0이 아닌동안 계속 기다림
* turn이 0이되면 while 문을 빠져나오고 임계구역에 접근
* 갔다오면 turn을 상대방 숫자로 바꿔줌(1)
* 문제점 : Progress 조건에 위배됨. 아무도 임계구역에 없을때 들어가야하는데…음..이 코드는 반드시 교대로 들어가게 되어있기 때문임
* 극단적으로 P0는 굉장히 자주 일어나고, P1은 딱 한번밖에 안일어난다면 P0는 P1이 턴을 넘겨주지 않는한 접근권한을 못 얻기 때문에, 두 프로세스가 비슷한 빈도수로 번갈아 일어날게 아니라면 사용하기 힘든 알고리즘이 

### 알고리즘 2 ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_10.png?raw=true)
 
* 프로세스가 각각 플래그를 하나씩 갖고 있음
* 일단 임계구역에 들어가고 싶으면 자기 플래그를 참으로 변경
* 그리고 상대방의 플래그를 체크해서 거짓일때까지 대기
* 쓰고 나온 뒤엔 자기 플래그를 거짓으로 변경해줌  
* 문제점 : Progress 조건에 위배. 둘다 깃발만 들었을땐 빈 임계구역에 아무도 못들어감. 즉 임계구역에 들어가고 난 뒤에 플래그를 거짓으로 바꾸기 때문임
  
### 알고리즘 3 - 피터슨 알고리즘 ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_11.png?raw=true)

* 알고리즘 1, 2를 합친 버전임
* 일단 플래그로 들어가겠다는 의사표현을 함 & 턴은 상대방에게 줌
* 상대방의 플래그가 참이고, 턴이 상대방이면 기다림
* 즉, 상대방의 플래그가 거짓이거나 턴이 내차례면 들어감
* 들어갔다나 나올땐 플래그를 거짓으로 바꿔줌
* 문제점 : Busy Waiting (Spin Lock) - while문을 계속 쓰면서 기다림 - CPU를 계속 잡고있음. 굉장히 비효율적으로 기다림.


### Synchronization Hardware ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_12.png?raw=true)
* 소프트웨어적인 동기화는, 한 줄의 코드를 동작하는 중에도 여러개의 인스트럭션으로 구성되기 때문에 실질적으로 한줄의 코드라고 해도 동시동작이 아닐 경우가 대다수라 CPU를 빼앗길 수 있기 때문에 인스트럭션 단위로 atomic하게 하드웨어단에서 동기화가 지원된다면 이 문제는 아~주 간단하게 해결할 수 있다
* Test_and_set(a) : a라는 데이터의 현재값을 읽고, 그 값을 1로 바꿔주는 동시 동작
* while(Test_and_Set(lock)) : lock이 0이면(unlock) 들어감과 동시에 1로 바꿈, lock이 1이면(lock) 기다림
	

### Semaphores ###
* 일종의 추상자료형
* 앞의 방식들을 추상화 시킴
* Semaphores S
	* 정수형
	* 아래의 두가지 atomic연산에 의해서만 접근 가능(동시연산)
		* P(S) :  자원을 획득하기
```c	
 	while(S<=0) do no-op;
		S—;
```

* 만약 S가 양수면, S는 감소하며 진입한다. 그렇지 않으면 양수가될때까지 기다린다(busy-wait)  
	* V(S) : 자원을 반납하기
```c
	S++;
```

### Critical Section of n Processes ###

```c
**Synchronization variable**
semaphore mutex; /* initially 1 : 1개가 CS에 들어갈 수 있다*/
 
**Process Pi**
do{
	P(mutex);
	critical section
	V(mutex);
	remainder section
}while(1);
```
* busy-wait는 효율적이지 못함(Spin lock) - 쓸데없이 CPU사용
* 그러니 Block & Wakeup 방식의 구현을 통해 해결해보자!(Sleep lock)
### Block / Wakeup Implementation ###
* Semaphore를 다음과 같이 정의
```c
 typedef struct
{ int value; /* semaphore */
  struct process *L; /* process wait queue */
} semaphore;
```
* block 과 wakekup을 다음과 같이 가정
	* block : 커널은 block을 호출한 프로세스를 suspend 시킴, 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
	* wakeup(P) : block된 프로세스 P를 wakeup시킴, 이 프로세스의 PCB를 ready queue로 옮김
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_13.png?raw=true)
 
* Semaphore 연산이 이제 다음과 같이 정의됨
```c
 P(S) : S.value—;  /* prepare to enter */ 
if(S.value < 0) /* Oops, negative, I can’t enter */
{ 
add this process to S.L;
block();
}
 
V(S) : S.value++;
 if(S.value <= 0) // 누군가가 자원을 기다리는 상태
{
remove a process P from S.L;
wakeup(P); // 다음 애를 깨워주기
}
```
	
### 어떤게 더 나을까? ###
* 임계구역의 길이가 긴 경우 block-wakeup이 적당(while문을 돌리는 시간이 길어지므로)
* 반대라면 block-wakeup의 오버헤드가 busy-wait의 오버헤드보다 더 커질 수 있음
* 일반적으로는 block-wakeup방식이 더 좋긴 하다
### 두가지 타입의 세마포어 ###
* Counting semaphore
	* 도메인이 0 이상인 임의의 정수값
	* 주로 리소스 카운팅에 사용
	* 자원의 갯수가 여러개인 경우
* Binary semaphore ( =mutex)
	* 0 또는 1값만 가질 수 있는 세마포어
	* 주로 mutual exclusion (lock/unlock)에 사용
### Deadlock & Starvation ###
* Deadlock : 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 이벤트를 무한히 기다리는 현상
* 예를 들어 S,Q를 둘 다 가져야 하는 그 프로세스 P0, P1가 있다고 치면…
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_14.png?raw=true)

* P0가 자기차례에 S를 획득하고 Q를 마저 획득하려는 찰나, CPU가 P1에 선점당하고 P1이 Q를 획득함
* 다시 P1차례가 되어도 Q를 가질 수 없어서 계속 기다려야함
* 해결법 : 자원 획득의 순서를 주자 (Q얻을 조건 : 일단 S를 얻어야 함)
* Starvation
	* Indefinite blocking : 프로세스가 suspend된 이유에 해당하는 세마포어 큐에서 빠져나갈 수 없는 현상
  
### 동기화와 관련된 고전적 문제들 ###
* **Bounded-Buffer Problem (****생산자****-****소비자 문제****)**
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_15.png?raw=true)

* 다수의 생산자가 비어있는 버퍼에 동시에 넣는경우 (소비자도 마찬가지)
* 혹은 버퍼가 유한하기 때문에 생기는 문제도 있음 - 버퍼는 다 찼는데 생산자가 도착했을 경우(생산자 입장에서는 자원이 없는 셈) - 소비자가 가져갈때까지 기다림
* 혹은 더이상 자원이 없는데 소비자가 있는 경우 - 생산자가 만들어줄때까지 기다림
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_16.png?raw=true) 
 
### **Readers-Writers Problem** ###
* 읽는 프로세스 & 쓰는 프로세스가 있을 때 
* 한 프로세스가 db에 write중일 때 다른 프로세스가 접근하면 안됨
* Read는 동시에 여럿이 해도 괜찮음
* 해결법
	* Writer가 db에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 reader들을 다 db에 접근하게 해준다
	* Writer는 대기중인 reader가 하나도 없을 때 비로소 db 접근이 허용된다
	* 일단 writer가 db에 접근중이면 reader들은 접근이 금지된다
	* Writer가 db에서 빠져나가야만 reader의 접근이 허용된다
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_17.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_18.png?raw=true)

 
* reader입장에서는 자기가 최초 reader일때 db에 lock을 걸고, 자기가 마지막 reader일 때 lock을 풀어준다.
* mutex는 readcount에 대해 lock을 걸기 위한 변수임
* writer가 지나치게 기다려서 starvation이 발생할 수 있음 
  
### **Dining-Philosohpers Problem** ###
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_19.png?raw=true)
* 양쪽에 젓가락 한짝씩 두고 쉐어하는 상황
* 철학자는 밥을 먹거나 생각하거나 하는 두가지 일을 함
* 왼쪽의 젓가락을 기다리는데, 오른쪽 사람이 또 젓가락을 가져감… 
* 즉 양쪽의 사람이 젓가락을 번갈아가며 쓴다면 젓가락 한쌍을 영영 획득할 수 없다.
* 혹은 모든 사람이 동시에 왼쪽 젓가락만(혹은 오른쪽 젓가락만) 가져간다면 모두가 굶게 됨
* 해결방안
	* 네명의 철학자만 테이블에 동시에 앉을 수 있도록 한다
	* 젓가락을 두개 모두 집을 수 있을 때에만 젓가락을 집을 수 있게 한다(일단 집지 말고)
	* 비대칭 : 짝수 철학자는 왼쪽 젓가락부터 집도록, 홀수 철학자는 오른쪽 젓가락부터 집도록
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_20.png?raw=true)
 
* self : 젓가락 잡을 권한이 있냐 없냐
* mutex : state에 대한 동시권한을 막는 lock
* test함수 실행 조건 : 왼쪽사람도 밥먹는중이 아니고, 오른쪽사람도 아니면서 나는 배고픈 상태일때 -> self : 젓가락 잡을 권한 획득
 
 
### Monitor ###
* 세마포어의 문제점
	* 코딩하기가 힘들다
	* 정확성의 입증이 어렵다
	* 자발적 협력이 필요하다
	* 한번의 실수가 모든 시스템에 치명적인 영향을 끼침
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_21.png?raw=true)
* 모니터 : 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_22.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_23.png?raw=true)

* 모니터는 애초에 동시접근이 안되기 때문에 따로 lock을 걸 필요가 없다 
* 모니터 내에서는 한번에 하나의 프로세스만이 활동 가능
* 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요가 없음
* 프로세스가 모니터 안에서 기다릴 수 있도록 하기 위해 condition variable 사용 : contidion x, y;
* Condition variable은 wait와 signal 연산에 의해서만 접근 가능 : 
	* x.wait(); x.wait()을 invoke한 프로세스는 다른 프로세스가 x.signal()을 invoke하기 전까지 suspend된다.
	* x.signal(); x.signal()은 정확하게 하나의 suspend된 프로세스를 resume한다. suspend된 프로그램이 없으면 아무일도 일어나지 않는다  
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/06_24.png?raw=true)
 
* 보통은 쉽게 세마포어 코드를 모니터 코드로 변경할 수 있다
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/6_25.png?raw=true)
 

------

본 포스팅은 [이화여대 반효경 교수님의 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323&ar=pop)를 기반으로 만들어졌습니다.  

문제시 삭제하도록 하겠습니다.  
