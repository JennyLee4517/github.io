---
title: "2일차 : Wrapper Class, 연산자, 제어문"
date: 2019-04-04 18:00:00 -0400
categories: Java



---

### Wrapper Class

- 기본형과 객체타입간의 전환을 위한 클래스들

| int    | Integer |
| ------ | ------- |
| long   | Long    |
| double | Double  |
| float  | Float   |
| cha    | Char    |

예전5.0 이하에서는 대부분의 메소드가 객체타입을 요구했기 때문에 자주 사용했었다    

하지만 5.0 부터는 오토박싱이라고 자동으로 객체화가 되기 때문에 많이 사용하지는않음  Integer.parseInt, Double.parseDouble 은 많이 사용함



## 연산자

| 산술 | + - * / %               |
| ---- | ----------------------- |
| 대입 | = += -= *= /= %=        |
| 관계 | > < >= <= == !=         |
| 논리 | &&(and) \|\|(or) !(not) |
| 증감 | ++ --                   |

| 조건(삼항)     | 조건?참값:거짓값;                            |
| -------------- | -------------------------------------------- |
| 조건여러개일때 | 조건1 ? 참: 조건2 ? 참 : 조건3 ? 참: 다거짓; |

### 증감

- ++a또는a++ : 현재 a값을 1증가함( a += 1 과같다)

- 수식에서 증감연산을 사용할 경우

  c = ++a + b ; a가 먼저 증가된 후 b를 더해서 c로 대입  

  c = a++ + b ; a+b를 c에 대입 후 a는마지막에1증가 

```java
package test.day0404;

public class OperatorEx1 {

  public static void main(String[] args) {

    //증감연산자
    int a = 3, b = 3;
    //단항일 경우에는 전치나 후치나 그게그거
    a++; 
    ++b;
    System.out.println("a : " + a + ", b : " + b);

    int c = 0;
    c = ++a * b--; //b가 후치라 c의 결과는 5 * 4 = 20
    System.out.println("a: " + a + ", b : " + b + ", c : " + c);

    a = c+++b; //c 후치로 계산됨 
    System.out.println("a: " + a + ", b : " + b + ", c : " + c);

    a = c+ ++b; //b의 전치로 계산됨 
    System.out.println("a: " + a + ", b : " + b + ", c : " + c);

    a = 5;
    System.out.println("a : " + a++); //출력 후 증가이므로 이제 a = 6
    System.out.println("a : " + ++a); //증가 후 출력
  }
}
```

```java
package test.day0404;

public class OperatorEx2 {

  public static void main(String[] args) {

    //산술, 대입 연산자
    int a, b, c, d, e;
    a = b = c = d = e = 5;
    a += 3;
    b -= 3;
    c *= 3;
    d /= 3;
    e %= 3;
    System.out.printf("a = %d, b = %d, c = %d, d = %d, e = %d", a, b, c, d, e);
  }
}
```

### 실습문제

> Scanner를 이용해서 급여를 입력받은후, 각 단위대로 갯수를 출력해보세요

```java
package test.day0404;
import java.util.Scanner;
public class OperatorTest1 {
  public static void main(String[] args) {
    // TODO Auto-generated method stub
    Scanner sc = new Scanner(System.in);

    int sal = 0;

    System.out.println("급여를 입력하세요.");
    sal = sc.nextInt();

    System.out.println("만원짜리 : " + sal/10000);
    System.out.println("천원짜리 : " + (sal%10000)/1000);
    System.out.println("백원짜리 : " + (sal%1000)/100);
    System.out.println("십원짜리 : " + (sal%100)/10);
    System.out.println("일원짜리 : " + sal%10);
  }
}
```

### 관계, 논리 연산자

- 결과로 boolean 타입을 리턴함
- 조건이 필요한 if문이나 반복문에 사용

```java
package test.day0404;

public class OperatorEx3 {

  public static void main(String[] args) {
    int kor = 67, eng = 90, mat = 60;
    System.out.println(kor>eng);//false
    System.out.println(kor>eng && eng>mat); 
    //두조건이 모두 참일때만 참이므로, 앞이 거짓이면 뒤에껀 보지도 않음(단회로 성질)
    //||일땐 앞이 참이면 뒤에껀 보지도 않음 
    System.out.println(kor<eng || eng>mat); //true 

    System.out.println(!(kor>eng)); //true
    System.out.println(kor==mat); //false
    System.out.println(kor != mat); //true
    if( kor >= 90 && eng >= 90 && mat >= 90 && (kor+eng+mat)/3 >= 93) {
      System.out.println("합격입니다.");
    }else {
      System.out.println("불합격입니다.");
    }
  }
}
```

### 조건 연산자 

- 조건 ? 참일때 : 거짓일때

> x = 5, y = 7 일 때  max에 큰값을 담고 싶으면

```java
if( x > 5){
  max = x ;
}else{
  max = y;
}
```

조건 연산자로 바꿔보면

```java
max = x > y ? x : y ;
```

조건 연산자의 장점 :  메소드나 html 태그 안에 inner로 삽입하여 사용가능

```java
println("x가 더" + ( x > y ? "크다" : "작다" ));
```



### 실습문제

> 1. 점수를 입력받은 후 90이상이면 "A"..."D" 까지 10점차이씩 주고 나머지는 "F"

```java
package test.day0404;
import java.util.Scanner;
public class OperatorEx5 {
  public static void main(String[] args) {
    // TODO Auto-generated method stub
    Scanner sc = new Scanner(System.in);
    int score;
    String grade = "";
    System.out.println("점수를 입력하세요.");
    score = sc.nextInt();
    System.out.println("당신의 학점은 " + 
                       (score>=90 ? "A" : score>=80 ? "B" : score>=70 ? "C" : score>=60 ? "D" : "F") 
                       + "입니다.");
  }
}
```

> 2. 태어난 연도를 입력하면 띠를 출력하세요
>
> - 서기1년이 닭띠다 => 2019 % 12 == 1 이면 닭띠
> - 즉, 2019 % 12 == 0 이면 원숭이띠

```java
package test.day0404;
import java.util.Scanner;
public class OperatorTest2 {
  public static void main(String[] args) {
    // TODO Auto-generated method stub

    Scanner sc = new Scanner(System.in);
    String animal = "";
    int year = 0;
    int m = 0;
    System.out.println("태어난 연도를 입력하세요.");
    year = sc.nextInt();
    m = year%12;
    animal = m==0 ? "원숭이" : m==1 ? "닭" : m==2 ? "개" : m==3 ? "돼지" : 
    m==4 ? "쥐" : m==5 ? "소" : m==6 ? "호랑이" : m==7 ? "토끼" : 
    m==8 ? "용" : m==9 ? "뱀" : m==10 ? "말" : m==11 ? "양" : "몰라";
    System.out.println("당신은 " + animal + "띠 입니다.");
  }
}
```



## 아스키코드

```java
package test.day0404;

public class AsciiCodeEx6 {

  public static void main(String[] args) {
    int a = 65;
    int b = 0x42; //16진수는 0x가 접두어. 8진수는 0이 접두
    char c = 'B';
    int d = 016;
    System.out.println(a + " , " + b + " , " + c + " , " + d );
    System.out.println((char)a + " , " + (char)b + " , " + (int)c);
    System.out.println('A' + 2);
    //기본형끼리는 서로 형변환으로 int <-> char 바꿔줄 수 있다.
  }
}
```



## 제어문

- if / switch / for / while / do-while

```java
package test.day0404;
public class loopEx7 {
  public static void main(String[] args) {

    int a = 65;
    while(a <= 90) { //조건이 true이면 반복됨.
      System.out.printf((char)a++ + " "); //후치이므로 출력 후 증가됨.
    }
    System.out.println();
    a = 'A';
    while(true) {
      System.out.printf((char)a++ + " ");
      if(a > 'Z')
        break; //현재 속한 가장 가까운 반복문을 빠져나간다.
    }
  }
}
```

```java
package test.day0404;

public class loopEx8 {

  public static void main(String[] args) {
    // TODO Auto-generated method stub
    int a = 0, b = 0 ;
    Exit: //label
    while(true) {
      System.out.println("Hello " + ++a);
      b=0;
      while(true) {
        System.out.println("kitty" + ++b);
        if(b==3) {
          //break;
          break Exit;
        }
      }
      // System.out.println("---------");
      // if(a==3)
      // break;

    }System.out.println("안녕");
  }
}
```

### 실습문제

> 점수를 입력을 하면 그 점수의 합계를 구하는 프로그램을 작성하시오  
>
> (단, 1~100 의 값이 아니면 다시 입력하고, 0이 입력되면 while문을 빠져나온다)

```java
package test.day0404;
import java.util.Scanner;
public class LoopTest3 {

  public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    int score = 0;
    int sum = 0;

    while(true) {

      System.out.println("숫자를 입력하세요.");
      score = sc.nextInt();

      if(score == 0) {
        System.out.println("프로그램을 종료합니다.");
        break;
      }else if(score<0 || score>100) {
        System.out.println("0~100사이의 값이 아닙니다.");
        continue;
      }

      sum += score;
      System.out.println("현재까지의 합계는 " + sum + "입니다.");
    }
  }
}
```



### For 문

- 형식1 : for(초기값 ; 조건식 ; 증감식)

  초기값이 일단 조건에 맞아야 반복을 시작함

- 형식2 : for(변수:배열 또는 제네릭변수) 

  자동으로 데이타갯수만큼 반복(5.0버전에 추가)

```java
package test.day0404;

public class ForEx11 {

  public static void main(String[] args) {
    // TODO Auto-generated method stub
    for(int i = 1 ; i <= 10 ; i++) 
      System.out.printf("%3d", i);
    System.out.println();
    //System.out.println(i); //i는 for문 안에서만 쓰이는 지역변수라서 error
    for(int i = 10 ; i >= 1 ; i--)
      System.out.printf("%3d", i);
    System.out.println();
    for(int i = 1 ; i <=10 ; i += 3)
      System.out.printf("%3d", i);
    System.out.println();

    int []arr = {5, 6, 4, 3, 7};
    for(int a:arr) //for문 형식2
      System.out.println(a);
  }
}
```

```java
package test.day0404;

public class ForEx12 {

  public static void main(String[] args) {

    //break & continue
    int s = 0;
    for(int i = 1 ; i <=20 ; i++) {
      if(i%2 ==0)
        continue; //짝수일 경우 i++로 이동
      s += i;
      System.out.println(s);
      if( i >=10 )
        break; //실제로는 20번 다 못돌고 11에서 빠져나옴 
    }

    System.out.println(s);
    //label연습

    hello:
    for(int i=1 ; i<=3 ; i++) {
      for(int j=1; j<=4 ; j++) {
        System.out.println(i + " , " + j);
        if(j==3)
          break hello;
        //break;
      }
    }
  }
}
```

### 실습문제

> ​	
>
> 1. 1부터 100까지의 합계 구하기 
> 2. 1부터 100까지의 짝수의 합과 홀수의 합을 모두 구하기 
> 3. 점수를 반복하여 입력하다가 0을 입력하면 빠져나오는데 0을 제외한 입력한 점수의 갯수 구하기 예) 총 5개 입력 
> 4. 다중 for문을 이용하여 5 x 5 별무늬 만들되 검은별이 사선으로 지나가는 무늬 만들어보기

```java
package test.day0404;
import java.util.Scanner;
public class Test4 {
  public static void main(String[] args) {
    // 문제1 
    int s1 = 0;
    for( int i = 1 ; i <= 100 ; i++ ) {
      s1 += i;
    }
    System.out.println(s1);

    //문제2
    int s2 = 0;
    int s3 = 0;
    for(int i = 1 ; i <= 100 ; i++) {
      if(i%2==0) {
        s2 += i;
      }else {
        s3 += i;
      }
    }
    System.out.println("짝수의 합은: " + s2);
    System.out.println("수의 합은: " + s3);

    //문제3
    Scanner sc = new Scanner(System.in);
    int score ;
    int scoreNum = 0;
    while(true) {
      System.out.println("점수를 입력하세요.");
      score = sc.nextInt();
      if(score == 0)
        break;
      scoreNum++;
    }
    System.out.println("총 " + scoreNum + "개 입력");

    //문제4
    for(int i = 1 ; i <= 5 ; i++ ) {
      for( int j = 1 ; j <= 5 ; j++) {
        if(i == j) {
          System.out.print("♥");
        }else {
          System.out.print("♡");
        }
      } 
      System.out.println("");
    }

  }}
```

