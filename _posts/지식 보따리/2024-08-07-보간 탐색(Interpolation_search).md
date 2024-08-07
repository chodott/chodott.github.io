---
title: 보간 탐색(Interpolation_Search)

categories:
  - 알고리즘
 
tags:
  - [Interpolation_Search]

date: 2024-08-07

toc: true
toc_sticky: true
sidebar_main: true
---

이 글은 작성자가 개인적으로 공부한 내용을 정리한 글입니다. <br>
따라서 사실과는 다른 내용이 포함될 수 있습니다.  <br>
잘못된 정보가 있다면 메일이나 댓글로 지적해주시면 정말 감사하겠습니다.
{: .notice--primary }


## <mark style = "background-color : #dcffe4"> 보간 탐색 개념

보간 탐색은 이진 탐색(Binary_Search)과 동일하게 **정렬된 리스트**에서 탐색 범위를 줄여가며 결과를 도출하는 알고리즘이다. <br>

이진 탐색과의 차이점은 탐색 범위를 정하는 방식이다.<br>

이진 탐색은 **리스트의 중간 지점**을 기준으로 탐색 범위를 결정한다.

보간 탐색은 이름처럼 보간 공식을 통해 찾고자하는 데이터가 있을 위치를 추측하여 탐색 범위를 결정한다.<br>
데이터의 위치를 추측하는 공식은 다음과 같이 유도한다.<br>

**<font color= 'green'>기울기</font> = (마지막 데이터값 - 첫번째 데이터값) / (마지막 인덱스 - 첫 인덱스)** <br>
**<font color= 'green'>탐색 위치</font> = 첫 인덱스 + (목표값 - 첫번째 데이터값) * (마지막 인덱스 - 첫 인덱스) / 기울기** <br>


## <mark style = "background-color : #dcffe4"> 구현 및 결과

이진 탐색과 보간 탐색의 동작 방식은 다음과 같이 동일하다.<br>
1. 탐색 위치를 구한다.
2. 탐색 위치의 데이터 값에 따라 범위를 좁힌다.
3. 종료 조건 달성 전까지 반복한다.

---

#### 1. 이진 탐색
```c++
int binary_search(vector<int>& num_vec, int start, int end, int target)
{
	//탐색 실패
	if (start > end) return -1;	
	//탐색 위치 선정
	int mid = (start + end) / 2;	
	//범위 설정
	if (target == num_vec[mid]) return mid;
	else if (target < num_vec[mid])
		return binary_search(num_vec, start, mid-1, target);
	else 
		return binary_search(num_vec, mid + 1, end, target);
}
```

#### 2. 보간 탐색
```c++
int interpolation_search(vector<int>& num_vec, int start, int end,int target)
{
	//탐색 실패
	if (num_vec[start] > target || num_vec[end] < target) return -1;
	//탐색 위치 선정
	int slope =  (num_vec[end] - num_vec[start]) / (end - start);
	int pos = start + (target - num_vec[start]) / slope;
	//범위 설정
	if (num_vec[pos] == target) return pos;
	else if (num_vec[pos] < target)
		return interpolation_search(num_vec, start, pos - 1, target);
	else 
		return interpolation_search(num_vec, pos + 1, end, target);
}
```

#### 결과
기울기가 일정한 데이터 예시를 사용했을 때<br>
이진 탐색은 평균 5.4 마이크로초가 소요되었고, 보간 탐색은 평균 2.2 마이크로초가 소요되었다.

예외의 경우도 시간 측정을 해보고 싶어서 다른 예제를 사용해보았다.<br>
rand함수를 이용해 10보다 작은 랜덤한 정수를 더해가며 num_vec의 데이터를 예제로 사용하였을 때<br>
**이진 탐색은 평균 6.2 마이크로초**가 소요되었고, **보간 탐색은 평균 2.5 마이크로초**가 소요되었다.

인접한 데이터 간의 차의 편차가 크지 않아서인지는 잘 모르겠지만 전반적으로 괜찮은 성능을 보였다.

