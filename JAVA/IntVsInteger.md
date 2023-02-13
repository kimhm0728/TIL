# int와 Integer의 차이 
#### int  
- primitive type
- 기본값이 0, null 값을 가지지 못함
#### Integer
- wrapper class
- 기본값이 null
# int  
int는 변수의 자료형 중 하나다.  
이 자료형은 기본형(primitive type)과 참조형(reference type)으로 나뉘는데, int는 기본형에 속한다.  
기본값으로 0을 가지고, null 값을 가질 수 없다.  
### primitive type 
종류로는 
_byte, short, int, long, float, double, char, boolean_
이 있다.  
변수에 값 자체를 저장한다는 특징을 가진다.  
### reference type
종류로는 
_class, interface, array, string_
등이 있다.   
변수에 메모리상 객체가 있는 위치(주소)를 저장한다.  
new 키워드를 이용해 객체를 생성한다.  
여기서 String은 참조형으로 주소를 저장하지만 기본형처럼 사용하는 특수한 자료형이다. 
# Integer
Integer는 클래스이기 때문에 참조형에 속한다.  
그중 기본형을 객체로 다루기 위해 사용하는 클래스인 
_wrapper class_
다.  
즉 Integer는 int의 wrapper class, Double은 double의 wrapper class가 되는 것이다.  
기본값으로 null 값을 가지며, 연산을 하기 위해서는 int로 unboxing해야 한다.
* boxing : primitive type -> wrapper class  
* unboxing : wrapper class -> primitive type  
(JDK 1.5부터 이것을 자동으로 해주는 오토박싱, 오토언박싱 기능이 포함되어 있다.)
# 메모리 차이  
null 값을 가져야 하는 경우가 아니라면 primitive type을 사용하는 것이 좋다.  
wrapper class는 primitive type보다 메모리 점유량이 더 높기 때문이다.  
</br>  
cf)  
dynamic programming의 memoization으로 문제를 풀 때,  
아직 구하지 않은 배열값인지 확인하고자 할 때 wrapper class의 null을 이용했었다.  
이 경우 코드는 간단하겠지만, 배열의 크기가 큰 경우에는 메모리 초과가 발생했다.  
즉 wrapper class보다 primitive type을 -1로 초기화하여 확인하는 것이 메모리 측면에서 효과적이다.   
