# 함수형 프로그래밍  
자료 처리를 수학적 함수의 계산으로 취급하고 상태와 가변 데이터를 멀리하는 프로그래밍 패러다임.  
## 특징
- immutable data  
- first-class citizen function  
## immutable data  
- immutable 객체: 객체가 가지고 있는 값을 변경할 수 없는 객체.
- 값이 변경될 경우 새로운 객체를 생성하여 반환해야 한다.  
- <-> mutable 객체: 객체의 값을 변경할 수 있는 객체  
## first-class citizen  
- 함수형 프로그래밍에서는 함수(function)가 일급 객체(first-class citizen)로 간주된다.  
- 일급 객체: 
- 변수나 데이터 구조안에 함수를 담을 수 있어서 함수의 파라미터로 전달하거나 함수의 반환값으로 사용할 수 잇다  
- 할당에 사용된 이름과 관계없이 고유한 구별이 가능하다.  
- 함수를 리터럴로 바로 정의할 수 있다.  
## Reactive Programming  
- 반응형 프로그래밍.  
- 선언형 프로그래밍이라고도 불리며, 명령형 프로그래밍의 반대말이다.  
- 함수형 프로그래밍을 활용한다.  
- 모든 것을 스트림(stream, 값들의 집합)으로 본다.  
- 제공되는 함수형 메서드를 통해 데이터를 immutable하게 관리할 수 있다.  

---
### 출처  
https://github.com/JaeYeopHan/Interview_Question_for_Beginner
