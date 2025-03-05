---
title:  "[C++] const_cast 연산자"
excerpt: ""

categories: [Language, C&#47;C&#43;&#43;]
tags: [C&#47;C&#43;&#43;]

toc: true
toc_sticky: true
 
date: 2024-10-04
last_modified_at: 2024-10-04
---

자료형을 변환할 때, C 언어는 **암시적 형변환과 명시적 형변환** 2가지가 존재합니다.  
간략하게 설명하면 암시적 형변환은 컴파일러가 대신 수행하는 형변환이고  
명시적 형변환은 반대로 프로그래머가 직접 형변환을 수행하는 것입니다.  

```c++
int i_num = 3.14;                   // 암시적

double d_num_01 = double(i_num);    // 명시적
double d_num_02 = (double)i_num;    // 명시적
```

그렇다면 C++ 는 형 변환 연산자가 무엇이 있을까요?  
C++ 에서 사용하는 형변환 연산자는 아래의 4가지가 있으며 모두 다른 역할을 수행합니다.  

```c++
static_cast<data_type>(Data);
const_cast<data_type>(Data);
dynamic_cast<data_type>(Data);
reinterpret_cast<data_type>(Data);
```

이번 포스트에서는 const_cast 연산자에 대해서 알아보겠습니다.  

<br/>

# const 키워드
---

먼저, C++ 에서 const는 **불변 상수** 를 의미합니다.  
자료형 앞에 const 를 작성하고 선언과 동시에 초기화 해줘야 사용할 수 있습니다.  
즉, const 를 붙인 데이터는 수정 불가능(read only)이라는 뜻입니다.  

const를 포인터와 함께 사용하면 포인터 자체 혹은 대상에 불변 성질을 부여할 수 있습니다.  

```c++
int num_01 = 1;
int num_02 = 2;

// const (int*)와 동일
const int* ptr_01 = &num_01;

// [Compile Error] : 'ptr_01'의 대상은 불변. (가르키는 메모리 변경 불가)
*ptr_01 = 10; 

// [Success] : num_01 자체가 const 속성을 갖는 것이 아니기 때문에 가능
num_01 = 10;  


// 포인터 자체가 상수가 됨
int* const ptr_02 = &num_02;

// [Success]
*ptr_02 = 10; 

// [Compile Error] : 포인터 자체가 불변이므로 내용 변경 불가능
ptr_02 = &num_03; 
```

<aside>
💡 만약 ‘num_0N’ 데이터들이 const int로 선언되었을 경우, 포인터는 반드시 const int*가 되어야 합니다.  
</aside>

const는 멤버 함수에도 사용할 수 있습니다.  

```c++
class Student 
{
private:
    int grade = 0;

public:
    int getGrade() const
	{
		int temp = 1;
		temp += 1;

        // [Compile Error]
		// grade += 1;

		return temp + grade;
	}
};
```

멤버 함수를 상수화(const) 시킬 경우,  
해당 함수 내부에서는 "모든 멤버 변수를 상수화 시킨다. 단, 지역변수는 예외로 한다." 라는 규칙이 적용됩니다.  

따라서 Student::getGrade( ) 함수에서 temp라는 지역 변수는 값의 수정이 가능지만  
멤버 변수인 grade의 값을 수정할 경우 Compile Error가 발생합니다.  

# const_cast
---

> const_cast<변환할 타입>(대상)

const_cast 는 포인터 혹은 참조형 데이터의 상수화(const)를 비상수화(non-const)로 변환시켜 줍니다.  
그 외에도 부여된 volatile 속성을 non-volatile로 변환할 때 사용되기도 합니다.  

```c++
char name[] = "Mgcllee";
const char* ptr_01 = name;

// [Fail]: ptr_01 이 const 속성을 갖고 있으므로 수정 불가
// ptr_01[0] = 'm';

char* ptr_02 = const_cast<char*>(ptr_01);

// [Success]: 비상수로 변환하였으므로 가능
ptr_02[0] = 'm';
cout << ptr_02; // mgcllee 출력


int age = 100;
const int& next_year_age = age;

// [Fail]: next_year_age 이 const 속성을 갖고 있으므로 수정 불가
// next_year_age += 1;

int& next_age = const_cast<int&>(next_year_age);

// [Success]: 비상수로 변환하였으므로 가능
next_age += 1;
cout << next_age;
```

<br/>

# 결론

const_cast 는 주로 상수성(const)을 제거하는데 사용됩니다.  
그렇기 때문에 다른 캐스팅 연산자처럼 전혀 다른 데이터 타입으로의 변환은 불가능합니다.  
대신 다른 캐스팅 연잔자는 상수성을 제거할 수 없습니다.  
<br/>
