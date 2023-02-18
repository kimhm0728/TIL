# Comparable과 Comparator  
Comparable과 Comparator의 공통점은 객체 비교를 위한 인터페이스라는 것이다.  
즉, 이 기능을 사용하기 위해서는 인터페이스 내 메소드를 Override를 통해 구현해야 한다.  

만약 직접 클래스를 만들고, 어떤 리스트에 이 클래스의 객체들이 담겨져있다고 해보자.  
이 리스트를 어떠한 기준으로 정렬하고자 할 때, List의 sort() 메소드나 Collections.sort() 메소드로 정렬할 수 없다.  
그럼 어떻게 정렬할 수 있을까?
이 경우 Comparable, Comparator를 사용한다.  

Comparable에는  compareTo(T o) 메소드가 선언되어 있다.  
또한 Comparator에는 compare(T o1, T o2) 메소드가 선언되어 있다.  
# compareTo()와 compare()  
이 두개의 메소드는 선언되어 있는 인터페이스도 다르지만, 매개변수의 개수도 다르다.  
compareTo는 매개변수가 하나다. 즉, 
_자기자신(this)와 매개변수를 비교_
한다.  
compare는 매개변수가 두개다. 즉, 자기자신과는 관련없이 _두 매개변수끼리 비교_
한다.  
# compareTo()  
math라는 변수가 존재하는 사용자 정의 클래스 Score가 있다고 할 때,  
math의 값을 기준으로 오름차순 정렬하고자 한다면 아래 코드처럼 작성한다. 
```java
	static class Score implements Comparable<Score> {
		int math;
		
		Score(int math) {
			this.math = math;
		}
		
		@Override
		public int compareTo(Score s) {
			if(this.math > s.math)
				return 1;
			else if(this.math == s.math)
				return 0;
			else
				return -1;
		}
	}
```
자신의 변수가 더 크다면 양수, 같다면 0, 작다면 음수를 반환한다.
또한 Comparable은 인터페이스이므로 implements 키워드를 사용한다.
```java
			return this.math - s.math;
```
compareTo 메소드 안을 이처럼 간결하게 표현할 수도 있다.  
자신의 값이 더 크다면 자신의 값에서 비교대상의 값을 뺐을 때 양수가 되고, 같다면 0, 작다면 음수가 되기 때문이다.  
# compare()  
같은 상황에서 자신이 아닌 매개변수 두개의 객체를 비교하는 compare를 작성해보자.  
```java
	static class Score implements Comparator<Score> {
		int math;
		
		Score(int math) {
			this.math = math;
		}
		
		@Override
		public int compare(Score s1, Score s2) {
			return s1.math - s2.math;
		}
	}
```
여기서 오름차순이 아닌 내림차순으로 정렬하고자 한다면 return 뒤 s1과 s2의 위치를 바꾸어주면 된다.  
직접 어떤 객체에서 compareTo/compare를 호출하여 비교할 수도 있고,  
정렬을 위해 Arrays.sort, List.sort, Collections.sort를 사용할 때 저절로 해당 메소드가 호출된다.  
또는 2차원 배열을 정렬할 때는 꼭 Comparator의 compare()를 구현해야 한다. 흔히 람다식을 통해 구현한다.  
###  람다식 예시
arr가 2차원 배열일 때, arr[i][1]을 기준으로 오름차순, arr[i][1]이 같다면 arr[i][0]을 기준으로 내림차순하는 람다식 코드다.
```java
    Arrays.sort(arr, (o1, o2) -> o1[1] == o2[1] ? o2[0] - o1[0] : o1[1] - o2[1]);
```
