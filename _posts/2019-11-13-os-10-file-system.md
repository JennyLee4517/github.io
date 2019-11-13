---
title: "[운영체제] 10. File System"
date: 2019-11-13 19:05:00 -0400
categories: CS
comments: true

---


## File and File System ##
### File ###
- A named collection of related information
- 일반적으로 비휘발성의 보조기억장치에 저장
- 운영체제는 다양한 저장장치를 파일이라는 동일한 논리적 단위로 볼 수 있게 해줌
- 연산 : create, read, write, reposition(lseek), delete, open, close 등
- open / close : 파일 자체를 메모리에 올리거나 내리는게 아니고 metadata를 올리거나 내림

### File attribute (Metadata) ###
- 파일 자체의 내용이 아니라 파일을 관리하기 위한 각종 정보들
- 파일 이름, 유형, 위치, 크기
- 접근권한, 시간, 소유자 등

### File system ###
- 운영체제에서 파일을 관리하는 부분
- 파일 및 파일의 메타데이타, 디렉토리 정보 등을 관리
- 파일의 저장 방법 결정
- 파일 보호 등

## Directory and Logical Disk ##
### Directory ###
- 파일의 메타데이타 중 일부를 보관하고 있는 일종의 특별한 파일
- 그 디렉토리에 속한 파일 이름 및 파일 attribute 들
- operation : search / create / delete / list / rename / traverse

### Partition (=Logical Disk) ###
- 하나의 물리적인 디스크 안에 여러 파티션을 두는게 일반적
- 여러개의 물리적인 디스크를 하나의 파티션으로 구성하기도
- 물리적 디스크를 파티션으로 구성한 뒤 각각의 파티션에 파일시스템을 깔거나 스와핑 등 다른 용도로 사용할 수 있음

## Open ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_01.png?raw=true) 
- 파일의 메타데이타를 메모리에 올린다

### open("a/b/c") ###
- 디스크로부터 파일 c의 메타데이터를 메모리로 가지고 오려면 어떤 과정을 거칠까?
- 이를 위해 directory path를 찾는다 : 루트 디렉토리 "/" open > 파일 a의 위치 획득 > 파일 a를 open > 파일 b의 위치 획득 > ... > 파일 c를 open
- 디렉토리 경로 찾기에 너무 많은 시간을 소요
- 오픈을 read / write 와 별도로 두는 이유가 이것 때문
- 한번 오픈한 파일은 read / write시 디렉토리 검색이 불필요

### Open file table ###
- 현재 오픈된 파일들의 메타데이터 보관소(메모리 상의)
- 프로세스별로 따로있는게 아니라 글로벌하게 관리된다
- 디스크의 메타데이타보다 몇가지 정보가 추가 : open 한 프로세스의 수, file offset(파일의 어느 위치에 접근 중인지 표시 - 이것만 프로세스별로 테이블 필요)

### File Descripter (file handle, file control block) ###
- Open file table에 대한 위치 정보(프로세스별)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_02.png?raw=true) 

- 최종 목표인 b의 내용은 os에 카피되고, 그 뒤에 프로세스에게 카피된다.
- 즉 다른 프로세스가 같은 내용을 읽어들인다면 반복할 필요 없이 OS는 그냥 복사해서 전달해주면 됨 (이걸 버퍼캐싱이라고 부른다)
- 단 서로다른 프로세스가 같은 파일을 오픈했다고 해도 서로 읽는 위치(offset)은 다를 수 있기 때문에 이 정보는 프로세스별로 관리된다(in PCB)
- open시 늘 시스템콜에 의해 OS가 호출되므로 OS는 모든 정보를 다 알 수 있으므로 LFU, LRU같은 알고리즘을 실제로 사용할 수 있게되는 것
- open 함수의 반환값은 해당 프로세스의 PCB에 b의 위치를 나타내는 값이 저장되어있는데(per-process file descriptor table) 그것의 인덱스(file descripter)임 

## File Protection ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_03.png?raw=true) 
- Access control Matrix는 희소행렬이 될 가능성이 높기때문에 연결리스트로 구현한다. 파일별 혹은 사용자별로 만든다.
- 근데 이 방법도 오버헤드가 크기 때문에 Grouping 방법을 쓴다 : 파일별로 9개의 비트만 있음 끝!
- 피피티 예시대로라면 111100100

## Mounting ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_04.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_05.png?raw=true) 


- 서로 다른 파티션의 파일시스템에 접근하는 방법

## Access Methods ##
- 시스템이 제공하는 파일 정보의 접근 방식

### 순차 접근(sequential access) ###
- 카세트테이프
- 읽거나 쓰면 offset은 자동적으로 증가

### 직접 접근 (direct access, random access) ###
- LP레코드 판과 같이 접근하도록 함
- 파일을 구성하는 레코드를 임의의 순서로 접근할 수 있음
- 직접 접근이라고 해도 파일 관리 방식에 따라 순차접근해야하는 경우도 있을 수 있음

## Allocation of File Data in Disk ##
- Contiguous Allocation (연속할당)
- Linked Allocation (연결할당)
- Indexed Allocation (색인할당)

## Contiguous Allocation ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_06.png?raw=true) 
- 임의 크기의 파일을 동일 크기의 블럭 단위로 나눠서 저장

### 단점 ###
- 외부조각이 생긴다
- File grow가 어려움
- 파일 생성시 얼마나 큰 hole을 배당할 것인가?
- 다시 말하지만 미래를 예측할 수 있다면 누구든 OPT 할 수 있다
- Grow를 가능하게 할것이냐 내부조각으로 낭비되게 할것이냐?

### 장점 ###
- 빠른 I/O
- 한번의 seek/rotation으로 많은 바이트를 전송
- Realtime file용으로, 또는 이미 실행중이던 프로세스의 swapping용으로 쓰자(공간효율성보다는 시간효율성이 더 중요하므로 - 어차피 임시자너)
- Direct Access 가능


## Linked Allocation ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_07.png?raw=true) 

### 장점 ###
- 외부조각이 발생하지 않는다

### 단점 ###
- 걍 배열과 연결리스트의 장단점을 생각해보면 간단하다
- 랜덤 접근이 안된다
- Reliability 문제 : 한 섹터가 고장나서 포인터가 유실된다면 ????
- 포인터를 위한 공간이 블럭의 일부가 되어 공간 효율성이 떨어진다
- 섹터당 512byte라면 그 중 4byte가 포인터에 쓰인다

### 변형 ###
- File-Allocation-Table(FAT) 파일 시스템
- 포인터를 별도의 위치에 보관하여 reliability와 공간효율성 문제를 해결
- 보통 2개 이상 복사해놓기 때문에 베드섹터 문제로부터 안전함

## Indexed Allocation ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_08.png?raw=true) 

- 섹터 하나를 희생시켜서 목차를 나열하게 한다

### 장점 ###
- 외부조각이 발생하지 않으면서 직접 접근도 가능~!

### 단점 ###
- 큰 파일이야 뭐.. 괜찮지만 작은파일인데도 굳이 색인용으로 섹터 하나를 빼는건 좀 공간낭비
- 혹은 파일이 진짜 엄~~청 크면 섹터 하나가지고 목차로 삼기에 부족할 수도 있다
- 해결 방안 : linked scheme - 마지막 목차는 다음목차를 가리킴 , multi-level-index - 2단계 페이지 테이블을 기억하세요


#### 자 여기까지는 이론적으로 이런식의 파일 저장법을 쓴다는거였고 이제 실제 쓰고 있는 현실적인 방법에 대해 알아보도록 하자 ####

## UNIX 파일시스템의 구조 ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_09.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_10.png?raw=true) 



### Inode list ### 
- 우리가 그동안 파일의 메타데이타는 디렉토리파일이 갖고 있는다고 배웠지만 유닉스에서는 디렉토리가 갖는 메타데이터는 일부(파일이름 & inode번호)일 뿐이고 대부분 여기에 담는다. 
- 파일 하나당 하나의 inode를 갖게 된다.
- 여러가지 방법으로 위치를 표현한다. 파일의 크기에 따라 적절한 방법으로 파일의 위치를 갖는다

## FAT File System ##
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_11.png?raw=true) 

- 포인터 정보를 FAT이 갖고 있는것이 특징
- 디렉토리 파일은 시작 블럭 정보만 갖고 있음


## Free-Space Management ##
- 비어있는 블럭은 어떻게 관리할 것인가?
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_12.png?raw=true) 

### Bit map or Bit vector ###
- 수퍼블럭 같은데에 1,0으로 표시해서 관리하는 방법

### Linked list ###
- 모든 빈 블럭을 링크로 연결
- 연속적인 가용공간을 찾는데에는 좀 어렵다
- 단, 공간의 낭비는 없다!

### Grouping ###
- 위 방법의 변형인데..
- 첫번째 빈 블락이 n개의 pointer를 가짐 > n-1 포인터는 빈 데이타 블락을 가리킴 > 마지막 포인터가 가리키는 블럭은 또다시 n pointer를 가짐
- linked index 비슷?

### Counting ###
- 프로그램들이 종종 여러개의 연속적인 블럭을 할당하고 반납한다는 성질에 착안
- first free block, # of contiguous free blocks를 유지(첫블럭부터 몇개까지 비어있는지~)

## Directory Implementation ##
### Linear list ###
- <file name, metadata>의 리스트
- 구현이 간단
- 디렉토리 내에 파일이 있는지 찾기 위해서는 linear search가 필요(시간소모)

### Hash Table ###
- linear list + hashing
- 해시테이블은 file name을 이 파일의 Linear list의 위치로 바꿔줌
- 탐색시간을 없앰 ^^! 역시 해쉬!
- 단 충돌이 발생할 가능성이 좀 있음
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_13.png?raw=true) 


### 메타데이터의 보관 위치 ###
- 디렉토리 내에 직접 보관하든지
- 디렉토리에는 포인터를 두고 다른 곳에 보관하든지(inode, FAT)

### 긴 파일 이름의 지원 ###
- <file name, metadata>의 리스트에서 각 엔트리는 일반적으로 고정된 크기를 가진다
- 파일 이름이 고정크기의 엔트리길이보다 길어지는 경우, 엔트리의 마지막 부분에 이름의 뒷부분이 위치한 곳의 포인터를 두는 방법이다
- 이름의 나머지 부분은 동일한 디렉토리 파일의 일부에 존재한다
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_14.png?raw=true) 



## VFS & NFS ##

### Virtual File System ###
- 서로 다른 다양한 파일시스템에 대해 동일한 시스템 콜 인터페이스(API)를 통해 접근할 수 있게 해주는 OS의 계층

### Network File System ###
- 분산 시스템에서는 네트워크를 통해 파일이 공유될 수 있음
- NFS는 분산 환경에서의 대표적인 파일 공유 방법

![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_15.png?raw=true) 


## Page Cache & Buffer Cache ##

### Page Cache ###
- 가상메모리의 페이징 시스템에서 사용하는 페이지프레임을 캐싱의 관점에서 설명하는 용어
- Memory-mapped I/O를 쓰는 경우 파일의 I/O에서도 페이지 캐시를 사용

### Memory-mapped I/O ###
- 파일의 일부를 가상메모리에 매핑시킴
- 매핑시킨 영역에 대한 메모리 접근 연산은 파일의 입출력을 수행하게함

### Buffer Cache ###
- 파일시스템을 통한 I/O연산은 메모리의 특정영역인 buffer cache 사용
- 파일 사용의 locality활용
- 한번 읽어온 블럭에 대한 후속 요청시 버퍼캐시에서 즉시 전달
- 모든 프로세스가 공용으로 사용
- LRU, LFU등의 교체 알고리즘 필요

### Unified Buffer Cache ###
- 최근의 운영체제(특히 리눅스)에서는 기존의 버퍼캐시가 페이지캐시에 통합됨
- 버퍼캐시도 페이지단위로 관리한다(512byte-> 4KB)

![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_16.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_17.png?raw=true) 


- memory-mapped I/O를 쓰면 이미 올라와있는 내용에 대해서는 OS의 도움을 받지않고 읽을 수 있음(OS를 호출하지 않아도 되서 속도가 빠름)
- 주의할점은 일관성 문제(readt-write는 어차피 카피라서 신경쓸필요x)
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_18.png?raw=true) 

- 2단원에서 이미 본 그림이다. 거기에서 언급하지 않았던 부분이 하나 있는데, 사용자 프로세스에서 code부분은 사실 read-only이기 때문에 swap-out 될때 swap area로 갈 필요가 전혀 없다. 이미 있는거 왜 또 만들어!
- 로더는 애초에 코드부분을 memory-mapped 해놓는다


![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_19.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_20.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_21.png?raw=true) 
![image](https://github.com/JennyLee4517/jennylee4517.github.io/blob/master/_posts/images/10_22.png?raw=true) 
 






------

본 포스트는 [이화여대 반효경 교수님의 운영체제 강의](http://www.kocw.net/home/search/kemView.do?kemId=1046323&ar=pop)를 기반으로 만들어졌습니다.  

문제시 삭제하도록 하겠습니다.  
