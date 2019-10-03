---
title: "[운영체제] 4. Process Management"
date: 2019-10-03 17:30:00 -0400
categories: CS
comments: true

---

* 프로세스의 생성
	* 부모 프로세스가 자식 프로세스를 생성(복제)
	* 프로세스의 트리 형성
	* 프로세스는 자원을 필요로 함
		* OS로부터 받던지
		* 부모와 공유
	* 자원의 공유
		* 부모와 자식이 모든 자원을 공유하는 모델
			* 보통은 이렇겐 안함. 부모와 자식은 경쟁관계가 됨
			* 리눅스 등 일부에서는 최대한 공유할 수 있는건 공유해서 메모리 낭비를 막기 때문. 당장 똑같은건 카피하지 않는다. 부모껄 공유. 내용이 수정되는 순간 카피 : Copy-on-write(COW) 기법
		* 일부를 공유하는 모델
		* 전혀 공유하지 않는 모델
	* 수행
		* 부모와 자식은 공존하며 수행되는 모델
		* 자식이 종료될때까지 부모가 기다리는(blocked) 모델
	* 주소 공간
		* 자식은 부모의 공간을 복사함(binary & OS data)
		* 자식은 그 공간에 새로운 프로그램을 올림
	* 유닉스의 예
		* fork() : 시스템 콜이 새로운 프로세스를 생성
			* 부모를 그대로 복사(OS data except PID + binary)
			* 주소 공간 할당
		* exec() : fork 다음에 이어지는 exec() 시스템 콜을 통해 새로운 프로그램을 메모리에 올림(덮어씌우기) - 꼭 하진 않을 수도 있음
* 프로세스의 종료
	* 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (exit)
		* 자식이 부모에게 output data를 보냄(via wait)
		* 프로세스의 각종 자원들이 운영체제에게 반납됨
	* 부모 프로세스가 자식의 수행을 종료시킴(abort)
		* 자식이 할당 자원의 한계치를 넘어섬
		* 자식에게 할당된 테스크가 더이상 필요하지 않음
		* 부모가 종료하는 경우
			* 운영체제는 부모 프로세스가 종료하는 경우 자식이 더이상 수행되도록 두지 않는다
			* 단계적으로 종료
 
* 프로세스와 관련한 시스템 콜
	* fork() : create a child(copy);
	* exec() : overlay new image;
	* wait() : sleep until child is done
	* exit() : frees all the resources, notify parent
 
* fork() 시스템 콜

![img](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/04_01.png?raw=true)
	* fork()실행 시점에서 자식 프로세스가 생성되며, PC도 그대로 복사되기 때문에, 그 자식프로세스는 main()함수를 첨부터 새로 실행하는게 아니라 fork()함수 이하부터 실행함 (그니까 fork 한다고 무한히 fork되는게 아님;;)
	* fork() 함수의 retrun 값을 통해 부모, 자식을 구분할 수 있다. 이걸로 부모 자식간 다른 일을 시킬 수 있음.
	* 헷갈리면 위의 코드를 두개 띄워놓고 부모/자식 나눠서 생각해보기! 

* exec() 시스템 콜
	* 완전히 새로운 프로세스로 태어나게 함
	![img](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/04_02.png?raw=true)

	* exec() 시스템콜을 해주게 되면 새로운 프로그램으로 덮어 씌워짐 
	* exec() 후엔 다시 되돌아 올 수 없다 
	* 물론 꼭 fork 후에 exec을 할 필요는 없다. (그렇게 되면 당연히 exec이후 코드는 실행이 불가능)


* wait() 시스템 콜
![img](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/04_03.png?raw=true)
	* block 상태로 잠재움 (자식이 죽을때까지)
 
* exit() 시스템 콜
	* 프로세스의 종료
		* 자발적 종료
			* 마지막 문장 수행 후 exit() 시스템콜을 통해
			* 명시적으로 적어주지 않아도 main 함수 리턴되는 위치에 컴파일러가 알아서 넣음
		* 비자발적 종료
			* 부모 프로세스가 자식 프로세스를 강제 종료
				* 자식 프로세스가 너무 많은 자원을 요청
				* 자식에게 할당된 테스크가 더이상 필요치 않음
			* 키보드로 kill, break 입력
			* 부모가 종료됨
				* 부모가 종료되기 전에 자식들이 먼저 종료됨
 
 
* 프로세스 간 협력
	* 독립적 프로세스
		* 프로게스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
	* 협력 프로세스
		* 프로세스 협력 매커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
	* 프로세스 간 협력 매커니즘(IPC : integerproces Communication)
		* 메시지를 전달하는 방법
			* Message passing : 커널을 통해 메시지 전달(둘이 직접 하는게 아님)
				* Message system : 프로세스 사이에 공유변수를 일체 사용하지 않고 통신하는 시스템
				* Direct Communication : 통신하려는 프로세스의 이름을 명시적으로 표시

				* Indirect Communication : mailbox(또는 포트)를 통해 메시지를 간접 전달
      
      * 주소공간을 공유하는 방법
        * Shared memory : 서로 다른 프로세스 간에도 일부 주소 공간을 공유하게 하는 매커니즘이 있음 - 물론 처음부턴 못하고 커널한테 한다고 시스템콜로 알려줘야..
        * 두 프로세스가 상당히 신뢰할 수 있는 관계여야만 가능
        ![img](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/04_04.png?raw=true)		
			
      * thread는 사실상 하나의 프로세스 이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 프로세스를 구성하는 스레드들 간에는 주소 공간을 공유하므로 협력이 가능
 

------

본 포스팅은 [이화여대 반효경 교수님의 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323&ar=pop)를 기반으로 만들어졌습니다.  

문제시 삭제하도록 하겠습니다.  
