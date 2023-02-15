# LinkedHashMap의 removeEldestEntry 메서드  
LinkedHashMap은 HashMap과 다르게 순서가 있는 컬렉션이다.  
객체에 값을 추가한 순서대로 저장되어 있다.  
이 메서드는 말그대로 가장 오래된(eldest) 값을 삭제한다는 것으로, LinkedHashMap만의 특수한 메서드다.  
map의 크기가 일정 크기를 넘으면, 가장 오래된 값을 삭제하고 새로운 값을 추가한다.  
기본적으로는 아래와 같이 정의되어 있다.
```java
  protected boolean removeEldestEntry(Map.Entry<K,V> eldest) {
      return false;
  }
```
이 메서드를 객체를 생성할 때 아래와 같이 오버라이드 해준다.
```java
   LinkedHashMap<K, V> map = new LinkedHashMap<K, V>() {
      @Override
      protected boolean removeEldestEntry(Entry<K, V> eldest) {
          return size() > 6;
      }
  };
```
여기서는 크기의 기준을 6으로 설정했다.  
어떤 새로운 값을 추가해 map의 크기가 6을 넘으면, 위의 조건이 true가 되어 가장 오래된 값이 삭제된다.  
즉, map의 크기가 항상 6 이하를 유지할 수 있는 것이다.  
상황에 맞게 조건을 바꿀 수도 있겠다.  
</br>  
이를 통해 페이지 교체 알고리즘 중 하나인 LRU 알고리즘을 구현할 수 있다.  
LRU 알고리즘은 버퍼의 크기가 정해져있고,  
찾고자 하는 페이지가 존재하지 않은 경우(miss) 가장 오래된 페이지를 삭제하고 새로운 페이지를 추가하는 알고리즘이기 때문이다.
