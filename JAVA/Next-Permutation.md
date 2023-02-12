# Next-Permutation 알고리즘  
순열을 사전 순서대로 뽑으려고 할 때, 지금 뽑은 순열의 다음 순열이 어떤 것인지 알아내는 알고리즘 방법  
순열을 구성하려는 집합에 중복되는 원소가 존재해도, DFS와는 다르게 중복없이 뽑을 수 있다  
</br>
예를 들어 {1, 2, 3}으로 순열을 구성하고자 할 때,  
123 -> 132 -> 213 -> 231 -> 312 -> 321 순서대로 순열이 구성됨을 알 수 있다.  
</br>
위 과정에서 규칙을 찾아보자.  
</br>
### 1. 뒤에서부터 증가하는 구간 찾기
231의 다음 순열을 구한다고 가정했을 때, 가장 뒤에서부터 1과 3을 비교한다.  
증가하지 않으므로 바로 왼쪽인 3과 2를 비교한다.  
증가하는 구간이므로 2 / 31을 기준으로 구간을 나눈다.  
</br>
만약 321 처럼 증가하는 구간이 존재하지 않는다면, 다음 순열이 없으므로 가장 마지막 순열인 것이다.  
</br>
### 2. 나눈 구간의 오른쪽부터 더 큰 것 찾고 교환하기
나눈 구간 2 / 31을 각각 왼쪽 구간, 오른쪽 구간이라고 해보자.  
왼쪽 구간의 가장 오른쪽 수와, 오른쪽 구간의 가장 오른쪽 수를 비교한다.  
이때 왼쪽 구간은 하나밖에 없으므로 2가 되고, 오른쪽 구간에는 1이 된다.  

2와 1을 비교했을 때 1이 더 크지 않으므로 오른쪽 구간의 수를 왼쪽으로 한칸 이동한다.  
2와 3을 비교했을 때 3이 더 크므로 2와 3을 swap해준다.  
</br>
### 3. 나눈 구간을 오름차순으로 정렬하기
이제 두개의 구간이 3 / 21이 되었다.  
마지막으로 오른쪽 구간을 오름차순으로 정렬해주면 끝이다.  
결과적으로 231의 다음 순열인 312이 구해졌음을 알 수 있다.  
</br>
# Next-Permutation Java Code  
위 과정을 코드로 작성하면 아래와 같다.  
arr[] 배열에 어떤 순열이 담겨져 있다고 할 때,  
다음 순열이 존재하면 arr[]에 다음 순열을 담고 true를 반환하고  
다음 순열이 존재하지 않으면 false를 반환하는 메서드다.  
</br>
```java  
	static boolean next_permutation(int n) {
		int idx = n - 1;
		
		while(idx > 0 && arr[idx] <= arr[idx - 1]) 
			idx--;
		
		if(idx == 0)
			return false;
		
		for(int i=n - 1;i>=idx;i--) {
			if(arr[idx - 1] < arr[i]) {
				char temp = arr[i];
				arr[i] = arr[idx - 1];
				arr[idx - 1] = temp;
				break;
			}
		}
		
		Arrays.sort(arr, idx, n);
		return true;
	}
```