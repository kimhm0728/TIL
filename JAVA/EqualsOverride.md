# equals() Method Override
equals()를 오버라이드 해야 하는 경우는 언제일까?  
바로 객체의 값끼리 비교해야 하는 경우다.  
<br>
만약 a, b 값을 가지는 클래스 Move가 있다고 했을 때  
이 a, b 값이 같다면 같은 객체라고 판단해야 하는 경우, equals를 재정의해야 할 필요가 있다.

```java
        Move m1 = new Move(3, 4);
        Move m2 = new Move(3, 4);
        
	static class Move {
		int a, b;
    
		Move(int a, int b) {
			this.a = a;
			this.b = b; 
		}
	}
```
코드로 표현하자면 위와 같다.  
객체 m1, m2 모두 a는 3, b는 4로 같은 값을 같지만  
equals 메서드를 통해 같은지 비교한다면 false를 return 할 것이다.  
객체는 각자만의 고유한 주소를 같기 때문이다.  

이 경우 m1, m2를 같은 객체로 인식하기 위해 equals를 재정의한다.

```java
		@Override
		public boolean equals(Object o) {
			Move m = (Move) o;
			return (m.a == this.a && m.b == this.b);
		}
```
위 코드를 Move 클래스 안에 넣는다면 a, b가 같은 값을 가진다면 같은 객체로 판단한다.  
필요에 따라 a만 같은 값을 가지는 등 다른 조건을 넣을 수 있다.  
</br>
# hashCode() Method Override
그럼 hashCode()는 언제 재정의해야 할까?  
바로 
*HashSet, HashMap, HashTable*
을 사용하는 경우다.  
</br>
이 세개의 컬렉션은 중복을 허용하지 않기 때문에  
contains, add, equals method를 호출할 때마다 동일한 객체가 이미 존재하는지 확인한다.  

어떻게 이미 존재하는지 여부를 판단할까?  
먼저 hashCode() 메서드를 호출하여 객체들의 해시 코드를 얻어낸다.  
이 해시 코드를 비교하여 같은 해시 코드를 가지는 객체가 있다면 equals() 메서드를 호출한다.  
equals() 메서드에서도 true를 반환한다면 이미 동일한 객체가 존재한다고 판단한다.  
</br> 
즉, hashCode() -> equals() 두 과정을 거치는 것이다.  
위에서 설명한 것처럼 동일한 값을 가지는 객체는 같은 객체라고 판단하기 위해서는 equals() 메서드를 오버라이드 해야 한다.  
하지만 HashSet, HashMap, HashTable에서는 equals() 메서드만을 오버라이드하면 안 된다.  
hashCode()에서 이미 다른 해시 코드를 반환해서 다른 객체라고 판단하기 때문이다.  
따라서 hashCode(), equals()를 모두 재정의해야 한다.

```java
	static class Move {
		int a, b;
		
		Move(int a, int b) {
			this.a = a;
			this.b = b; 
		}
		
		@Override
		public int hashCode() {
			return (""+a+b).hashCode();
		}
		
		@Override
		public boolean equals(Object o) {
			Move m = (Move) o;
			return (m.a == this.a && m.b == this.b);
		}
	}
```  
```java
HashSet<Move> set = new HashSet<>();  
```
Move 객체를 저장하는 HashSet에서 Move의 a, b값을 기준으로 중복 여부를 판단하고 싶다면 위처럼 재정의한다.  
어차피 equals에서 a, b 값을 비교하기 때문에  
hashCode에서는 "".hashCode() 처럼 언제나 같은 값을 반환할 수 있도록 작성해도 된다.  
하지만 불필요한 equals 메서드 호출을 방지하기 위해 위 처럼 작성하는 것이 시간 측면에서도 좋다.  
  
</br>  
_BFS, DFS 문제 등을 풀 때 방문 여부를 확인하는 boolean[][] visit 함수의 크기가 너무 커 최대 힙 크기를 벗어날 때, HashSet을 통해 방문 여부를 확인할 수 있다.
이 경우 hashCode()와 equals() 메서드를 재정의한다.  
또한 HashSet, HashMap, HashTable의 add() 메서드는 이미 객체가 존재하는 경우 false, 존재하지 않는 경우 true를 반환하기 때문에  
contains()와 add() 메서드를 각각 호출하지 않고 add() 메서드 한 번의 호출만으로 방문 여부를 확인할 수 있다._
