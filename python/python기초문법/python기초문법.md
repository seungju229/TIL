# Sequence Types

- 여러 개의 값들을 **순서대로 나열**하여 저장하는 자료형
    - str, list, tuple, range

## list

- 여러 개의 값을 순서대로 저장하는 **변경 가능한** 시퀀스 자료형
    
    ```python
    my_list = [1, 2, 3]
    my_list[0] = 100
    print(my_list) #[100, 2, 3]
    ```
    
- 대괄호( [] )로 표기
- 데이터는 어떤 자료형도 저장할 수 있음.
    
    ```python
    my_list_3 = [1, 2, 3, 'Python', ['hello', 'world', '!!!']]
    print(len(my_list)) #5
    print(my_list[3][1]) #!!!
    print(my_list[3][1][-1]) #w
    ```
    

## tuple

- 여러 개의 값을 순서대로 저장하는 **변경 불가능한** 시퀀스 자료형
- 소괄호( () )로 표기
- 데이터는 어떤 자료형도 저장할 수 있음

```
my_tuple_1 = ()r
my_tuple_2 = (1,) #, 없으면 int가 됨. 하나일때는 , 붙여야됨.
my_tuple_3 = (1, 'a', 3, 'b', 5)
```

- 직접 사용하는 경우는 없음

## range

- 연속된 정수 시퀀스를 **생성**하는 **변경 불가능**한 자료형
- range(시작 값, 끝 값, 증가 값)
    - 증가 값이 없으면 1씩 증가
    - 증가 값이 음수면 감소(시작 값이 끝 값보다 커야됨)
    - 증가 값이 양수면 증가(시작 값이 끝 값보다 작아야됨)
    - 증가 값이 0이면 에러
- range(n)
    - 0부터 n-1까지 숫자의 시퀀스
    - range(10) 은 0부터 9까지의 시퀀스
- range(n, m)
    - n부터 m-1까지 숫자의 시퀀스

# Non-sequence types

## dict(딕셔너리)

- **key-value 쌍**으로 이루어진 **순서와 중복이 없는** **변경 가능한** 자료형
- key는 변경 불가능한 자료형만 가능(str, int, float, tuple, range..)
- value는 모든 자료형 사용 가능
- 중괄호( {} )로 표기
- 

```
my_dict = {'apple': 12, 'list': [1, 2, 3]} #여기서 요소는 2개. 0번째는 없음. 순서가 없으니까
print(my_dict['apple'])
```

## set

- **순서와 중복이 없는** **변경 가능**한 자료형
- 수학에서의 집합과 동일한 연산 처리 가능
- 중괄호( {} )로 표기
    - 빈 세트는 set()로 표기. dict랑 겹치니까

# Other Types

## None

- 파이썬에서 ‘값이 없음’을 표현하는 자료형

## Boolean

- 참과 거짓을 표현하는 자료형
- 비교/논리 연산의 평가 결과로 사용됨
- 주로 조건/반복문과 함께 사용
- T, F 대문자로 해야됨.

## collection

- 여러 개의 항목 또는 요소를 담는 자료 구조(str, list, tuple, set, dict)

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/13947524-5760-4c7b-911f-90194802f0ff/Untitled.png)

## type conversion(형변환)

- 한 데이터 타입을 다른 데이터 타입으로 변환하는 과정
- 암시적 형변환 / 명시적 형변환
    - 암시적 형변환 : 파이썬이 자동으로 수행하는 형변환
        - boolean과 numeric type에서만 가능
        - true →1, false →0
    - 명시적 형변환 : 프로그래머가 직접 지정하는 형변환. 암시적 형변환이 아닌 경우 모두 포함.
    - 의도가 담기지 않은 코드가 포함되어 있으면 가독성이 떨어지기 때문에 지양해야함.
    

## 복합연산자

- 연산과 할당이 함께 이뤄짐.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/2cb76a57-1f7a-4c09-b2a7-48470bea6beb/Untitled.png)

## 비교연산자

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/1a5b2bca-9c7c-4a9d-b553-9a8142d9c9ac/c756019e-d263-4d83-a3e9-d5b9611c3910/Untitled.png)

- !는 보통 부정의 의미
- ==는 동등성(단순히 값 비교). is는 식별성 (주소도 같아야됨)
    
    ```
    print(1 is True)  # False
    print(2 is 2.0)  # False
    print(1 == True)  # True
    print(2 == 2.0)  # True    #is는 보통 none, boolean 비교할 때 사용
    ```
    

## 논리 연산자

- and

![dog-8198719_640](https://github.com/user-attachments/assets/da662b36-533b-4315-9d08-a97886022dbb)

## 단축평가

- 논리 연산에서 두번째 피연산자를 평가하지 않고 결과를 결정하는 동작

```python
vowels = 'aeiou'
print(('a' and 'b') in vowels)  
#F, a먼저 평가하는데 a 값이 있으니까 b로 넘어감. b는 없으니까 F

print(('b' and 'a') in vowels) 
 #T, 최종 코드는 a in vowels

print(3 and 5)  # 5 ,5가 나온거는 평가가 끝까지 갔다는 것
print(3 and 0)  # 0
print(0 and 3)  # 0, 이미 첫번째에서 false니까 and 연산자에서는 무조건 false여서 뒤에 3 볼 필요가 없음.
#(단축평가)
print(0 and 0)  # 0
#단축평가

print(5 or 3)  # 5 , 이미 앞에서 true인데 or연산자는 상관없으니까 뒤에 안봄.
print(3 or 0)  # 3
print(0 or 3)  # 3, 0은 false지만 뒤에 수에 따라서 true가 될 수 있는 여지가 있어서 단축평가 안함.
print(0 or 0)  # 0, false여도 끝까지 봐야됨. 단축평가
```

- 코드 실행을 최적화하고, 불필요한 연산을 피할 수 있도록 하기 위해 단축평가 시행

## 멤버십 연산자

- 특정 값이 시퀀스나 다른 컬렉션에 속하는지 여부를 확인
- in
    - 왼쪽 피연산자가 오른쪽 피연산자의 시퀀스에 속하는지를 확인
- not in
    - 왼쪽 피연산자가 오른쪽 피연산자의 시퀀스에 속하지 않는지를 확인

- 딕셔너리에서 보려고 하면 key in 딕셔너리

## 시퀀스형 연산자

- +
    - 결합 연산자
- *
    - 반복 연산자

## 연산자 우선순위

- 외울필요는 없음.