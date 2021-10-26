---
title: "소스코드 메트릭(Code Metrics) 개요"
excerpt: "소프트웨어 품질향상을 위한 소스코드 메트릭(Code Metrics)를 소개한다."
categories:
  - Code Metrics
  - SW Test
last_modified_at: 2020-07-15
layout: post
---
- 소프트웨어 품질 향상을 위한 소스코드 메트릭(Code Metrics)를 소개한다.



- 소프트웨어의 복잡도 감소, 유지보수 용이성 증대 등 소프트웨어 품질향상을 위한 소스 코드의 품질 측정지표다.
- 다음 메트릭들은 방위사업청에서 배포한 '방위사업청 매뉴얼 제2020-8호 부록(무기체계 소프트웨어 개발 및 관리 매뉴얼)'에서 지정한 메트릭 기준표다.

![image](/assets/img/2020-07-15-Code Metrics1/image1.png)



## Cyclomatic Complexity
- 의미: 함수 내 분기문의 개수
- 계산 방법: 함수 내 분기문의 개수 + 1
- 해결 방법: 복잡한 모듈 안의 속성, 함수들을 다른 모듈로 분리함으로서 복잡도를 하락시킨다.

ex) 다음 예제 코드의 'Cyclomatic Complexity'는 3이 된다.<br>
'switch 문' 복잡도 1 + 'case S_sleep 내의 if 문' 복잡도 1 + '기본 복잡도' 1 = 3이 된다.

```c
#include <stdint.h>

void foo(state process_state) {
	static int sleep_count = 0;

	switch (process_state) {
	case S_init:

	case S_run:

	case S_sleep:
		sleep(100);

		if (sleep_count > 5) {
			sleep(10);
			sleep_count = 0;
		} else {
                                  sleep_count++;
                       }
	default:
		process_state = S_init;
	}
}
```



## Number of Call Levels
- 의미: 함수 내 조건문의 최대 중첩 깊이
- 계산 방법: 프로그램을 제어 흐름 그래프로 표현 후 그래프의 높이
- 해결 방법: 복잡한 분기문의 경우 새로운 함수를 생성하여 분리시킨다.

ex) 다음 예제 코드의 'Number of Call Levels'는 4가 된다.<br>
처음 if문의 중첩 깊이가 4 그리고 다음 if문의 중첩 깊이가 2지만, if문의 최대 중첩 깊이가 4이기 때문이다. <br>
'Cyclomatic Complexity'는 'if문의 개수' 복잡도 6 + 기본 복잡도 1 = 7이 된다.

```c
void foo(void) {
	if(1) {
		if(1) {
			if(1) {
				if(1) {

				}
			}
		}
	}

	if(1) {
		if(1) {

		}
	}
}
```



## Number of Function Parameters
- 의미: 함수의 매개변수 개수
- 계산 방법: 함수 호출 시 사용되는 인자의 개수
- 해결 방법: 너무 많은 인자를 사용하는 경우 자료구조를 사용하고 사용하지 않는 인자는 삭제한다.

ex) 다음 예제 코드의 'Number of Function Parameters'는 10이 된다. <br>
사용하는 매개변수의 개수가 많아 지면 자료 구조를 사용하여 매개변수 개수를 감소시켜야 한다.

```c
void foo(int arg1,
         int arg2,
         int arg3,
         int arg4,
         int arg5,
         int arg6,
         int arg7,
         int arg8,
         int arg9,
         int arg10) {

}
```



## Number of Calling Functions
- 의미: 함수 외부에서 함수를 호출하는 횟수
- 계산 방법: 함수 외부에서 해당 함수를 호출한 횟수
- 해결 방법: 자주 호출되는 함수는 상위 모듈의 코드 일부로 합쳐야 한다.

ex) 다음 예제 코드의 'Number of Calling Function'은 foo: 3, boo: 2, poo: 1이 된다.

```c
void foo(void) {

}

void boo(void) {
	foo();
}

void poo(void){
	boo();
	foo();
}

int main(void) {
	foo();
	boo();
	poo();
}
```



## Number of Called Functions
- 의미: 함수에서 다른 함수를 호출하는 횟수
- 계산 방법: 함수 내 다른 함수를 호출한 횟수 (같은 함수를 호출하는 경우는 1로 계산)
- 하위 모듈에서만 사용되는 함수는 하위 모듈의 코드 일부로 합쳐야 한다.

ex) 다음 예제 코드의 'Number of Called Function'은 main: 3, poo: 2, boo: 1, foo: 0이 된다.<br>
poo: 2가 되는 이유는 foo 함수는 여러번 호출하여도 1번 호출한 것으로 계산하기 때문이다.

```c
void foo(void) {

}

void boo(void) {
	foo();
}

void poo(void){
	boo();
	foo();
	foo();
	foo();
	foo();
}

int main(void) {
	foo();
	boo();
	poo();
}
```



## Number of Executable Code Lines
- 의미: 함수 내 실행 가능한 코드 라인 수
- 계산 방법: 중괄호([ ]), 빈 문장( ), 선언문(변수 선언 및 초기화), 레이블을 제외한 세미콜론(;) 또는 조건문(if, 단 else는 제외한다.)로 마치는 코드 라인 수
- 해결 방법: 함수 내 불필요한 코드를 삭제한다.

ex) 다음 예제 코드의 'Number of Executable Code Lines'는 7이 된다. <br>
for 반복문은 세미콜론이 두 개이므로 실행되 코드 라인은 두 개로 계산된다.

```c
#include <stdint.h>

#define COUNT 0

void foo(int n) {
	int i = 0;
	int last = n / 2;
	if (n <= 1)
	{
		return 0;
	}

	for (i = 2; i <= last; i++)
	{
		if ((n % i) == 0)
		{
			return 0;
		}
	}

	return 1;
}
```

- foo 함수 내에서 실행 가능한 라인 수는 다음과 같이 6이다.

```c
if (n <= 1)
return 0;
for (i = 2; i <= last; i++)
if ((n % i) == 0)
return 0;
return 1;
```

출처: <https://m.blog.naver.com/PostView.nhn?blogId=suresofttech&logNo=221114801984&proxyReferer=https%3A%2F%2Fwww.google.com%2F><br>
방위사업청에서 배포한 '부록(무기체계 소프트웨어 개발 및 관리 매뉴얼)'
