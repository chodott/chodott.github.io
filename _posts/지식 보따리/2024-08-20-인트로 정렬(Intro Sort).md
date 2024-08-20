---
title: 인트로 정렬(Intro Sort)

categories:
  - 알고리즘
 
tags:
  - [Intro Sort]

date: 2024-08-20

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 작성자가 개인적으로 공부한 내용을 정리한 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다.  <br>
잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 정말 감사하겠습니다.
{: .notice--primary }


## <mark style = "background-color : #dcffe4"> 개념

평균적으로 빠른 성능을 내면서 최악의 조건에서도 O(nlogn)의 시간복잡도를 가지는 하이브리드 정렬 알고리즘이다.<br>
3가지 알고리즘(퀵 정렬, 힙 정렬, 삽입 정렬)을 결합하였다.<br>

힙 정렬은 이론적으로 퀵 정렬과 같은 O(nlogn)의 시간 복잡도를 가지지만, <br>
힙 구조로 변환하는 과정에서 발생하는 무작위 메모리 접근으로 인한 성능저하가 발생할 수 있다.<br>

인트로 정렬은 이러한 힙 정렬의 단점을 극복해 퀵 정렬의 성능을 최대한 활용하면서,<br>
퀵 정렬의 재귀깊이가 깊어질 때에는 힙 정렬로 전환하여 사용하는 것으로<br>
퀵 정렬의 최악의 경우 O(n^2)의 시간 복잡도가 소요되는 단점을 극복한다<br>
따라서 전체적으로 안정적이고 빠른 정렬 성능을 제공할 수 있게 된다.! 

### 동작 과정
1. 배열의 크기가 작은 경우(일반적으로 16), 삽입 정렬을 사용하여 배열을 정렬한다.<br>
(삽입 정렬은 평균 O(n^2)의 시간복잡도를 가지지만, n이 작을 경우 O(n)과 크게 차이가 없다.)<br>

2. 퀵 정렬을 수행한다. (피봇 값은 데이터의 마지막 값으로 설정)

3. 퀵 정렬의 **재귀호출이 임계값을 초과하면**(일반적으로 logN), **힙 정렬을 사용**하여 마저 정렬한다.<br>
(힙 정렬은 최악의 경우 O(nlogn)의 시간복잡도를 가진다. 따라서 퀵 정렬의 최악의 경우 O(n^2)의 시간복잡도를 피한다)<br>

4. 분할하는 과정에서 배열의 크기가 충분히 작아지면 삽입 정렬을 사용한다.

---

## <mark style = "background-color : #dcffe4"> 구현 및 결과

### 1. 삽입 정렬
```c++
void insertionSort(vector<int>& data_vec, int left, int right)
{
	for (int i = left+1; i <= right; ++i)
	{
		int value = data_vec[i];
		int j = i;
		for (j = i; j > left; --j)
		{
			if (data_vec[j - 1] > value) data_vec[j] = data_vec[j - 1];
			else break;
		}
		data_vec[j] = value;
	}
}
```

### 2. 힙 정렬
```c++
void heapSort(vector<int>& data_vec, int left, int right)
{
	make_heap(data_vec.begin() + left, data_vec.begin() + right + 1);
	sort_heap(data_vec.begin() + left, data_vec.begin() + right + 1);
}
```

### 3. 퀵 정렬
```c++
int partition(vector<int>& data_vec, int left, int right)
{
	int i = left; int j = left;
	int& pivot = data_vec[right];
	for (; j < right; ++j)
	{
		if (data_vec[j] < pivot)
			swap(data_vec[i++], data_vec[j]);
	}
	swap(data_vec[i], pivot);

	return i;
}

void quickSort(vector<int>& data_vec, int left, int right)
{
	if (left >= right) return;
	int pivot = partition(data_vec, left, right);
	quickSort(data_vec, left, pivot-1);
	quickSort(data_vec, pivot+1, right);
}
```
### 4. 인트로 정렬

```c++
void introSort(vector<int>& data_vec, int left, int right, int depth)
{
	if ((right - left)  < 16) insertionSort(data_vec, left, right);
	else if (depth == 0) heapSort(data_vec, left, right);
	else
	{
		int pivot = partition(data_vec, left, right);
		introSort(data_vec, left, pivot-1, depth - 1);
		introSort(data_vec, pivot + 1, right, depth - 1);
	}
}
```

### 결과

1. 일반적인 데이터의 경우(무작위 데이터)<br>
![image](https://github.com/user-attachments/assets/700dee24-e6f5-43c3-82a4-6a56a1544df9)<br>
<br>평균 시간복잡도가 O(n^2)인 삽입 정렬만 특히 시간이 오래 소요되는 것을 확인할 수 있었다.

2. 동일한 데이터 배열의 경우<br>
![image](https://github.com/user-attachments/assets/2d62207a-9e73-46c6-a3b7-ec593928081a)<br><br>
**퀵 정렬에서의 최악의 경우 O(n^2)의 시간복잡도를 극복했는지** 측정을 위해 사용.<br>
이미 정렬이 되어있기 때문에 삽입 정렬에서 가장 빠른 시간이 소요됨을 확인 할 수 있고,<br>
퀵 정렬에서 특히 시간이 더 많이 소요됨을 확인할 수 있다. 또한 데이터의 크기를 더 증가시키면<br>
더 크게 증가하는 모습을 볼 수 있었다.



