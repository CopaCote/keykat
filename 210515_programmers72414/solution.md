<span style="font-family:Lato,PingFang SC,Microsoft YaHei,sans-serif">

## programmers 72414 - 광고 삽입


### 문제 
<b>https://programmers.co.kr/learn/courses/30/lessons/72414</b>


<br/><br/><br/><br/><br/><br/>


### 문제 풀이<b>

문자열 처리 + dp 문제
<br/>

- 까만 줄은 어떤 사람이 비디오를 보고 있는 시간 간격
- 파란 줄은 시간축 (x축이라고 생각하겠음)
- 빨간 줄은 중간 광고가 방영되는 시간 간격

간단하게 문제 풀이를 설명하자면,

1. 시간 문자열을 전부 초로 변환
2. 구간 별로 몇 명 있는지 구하기
3. 구간 별로 어디에 광고를 삽입하면 좋은 지 구하기

<br/><br/><br/>

### 첫번째 오류 : 시간 구간에 대한 잘못된 생각

<br/>

일단 주어진 시간을 전부 초로 환산하고 배열에 뿌려준다.
만약 logs에 있는 시간이 01:00:00 ~ 02:00:00이라고 한다면, 1 * 3600 ~ 2 * 3600 즉 3600초 ~ 7200초 사이가 된다는 것.

 = 즉 처음 시작 시간인 0초를 기준으로 어떤 사람은 3600초가 흐른 시점부터 7200초가 흐른 시점까지 비디오를 본다는 뜻.

 = 즉 3600초 동안 비디오를 본다는 뜻.

<br/>

결국 (시작 시간 0초 기준으로) 어떤 시간 축에서 특정 시작 지점으로부터 특정 끝 지점까지 몇 명의 사람이 비디오를 보고 있는지 센다고 할 때,
이 사람은 3600초부터 비디오를 보기 시작해서 3601초, 3602초, 3603초 ... ~ 7197초, 7198초, 7199초, 7200초 지점에 이 사람이 존재하므로 각 지점마다 한명씩 늘려준다.

<br/><br/><br/>

그림을 기준으로 1번 사람은 01:20:15 ~ 01:45:14 동안 비디오를 보고 있으니까,
1 * 3600 + 20 * 60 + 15 ~ 1 * 3600 + 45 * 60 + 14
=> 4815초 ~ 6314초의 시간 동안 비디오를 보고 있다는 얘기
즉 4816초 ~ 6314초의 지점에 1명씩 추가한다.

time[4816] + 1
time[4817] + 1
...
time[6314] + 1

이렇게 하면 어떤 겹치는 지점에서는 한 인덱스에 여러 명이 들어갈 수 있게 되는 것.

그런데 위에서 왜 4815 ~ 6314인데 추가는 4816 ~ 6314를 추가할까? 한다면, 4815초 시점은 얘가 영상을 보기 시작한 시점이지, 보고 있는 시점이 아님. 즉 4815 -> 4816초로 가는 1초의 시간 동안 영상을 본 것이므로 4816초부터 세게 됨.

문제 자체가 누적 시간을 구하는 것이기 때문에 특정 시간 점에서 몇명이 있는 걸 구하는게 아니라, 특정 구간에 몇명의 인원이 있는지를 구하는 것임. 이거 때문에 6문제 정도가 오답 처리가 되더라.

<br/>

```
for(int j = stime + 1; j <= etime; j++){
    ttime[j]++;
}
```
결국 이렇게 시작 지점에서 +1 된 구간부터 끝지점까지 사람을 추가해야 함.


<br/><br/>

#### 두번째 오류 : 완전 탐색으로 인한 시간 초과

문제를 좀 대충 대충 푼 것도 있는데, logs의 범위가 30만개다. 당연하게 2중첩만 해도 10초가 훌딱 넘어가며, 실제로 완전 탐색으로 했을 경우 5초, 8초 안으로 간신히 들어오는 경우도 있지만, 시간이 터지는 경우도 있었다.

즉 위에서 만든 시간 구간 배열을 dp로 처리해서 시간을 단축해야 한다. 위의 배열은 특정 지점에서 몇명의 사람이 비디오를 보고 있는지를 저장한 것인데, 이를 잘 응용하면 0초 부터 n초 까지 총 몇명의 사람이 비디오를 보고 있는지로 만들 수 있다.

가령 time = [0,1,1,1,0,0,2,2,3,3,3,3]이라고 하면
0초 시점에는 0명, 1초 시점에는 1명... 이렇게 되니까

dp[0] = 0
dp[1] = time[0] + time[1]
dp[2] = time[0] + time[1] + time[2]
...

이렇게 된다.
따라서 특정 구간에선 총 몇 명이 있는지를 알려면 dp[끝] - dp[시작]으로 해서 구간을 구해주면 된다.

```
dp[0] = 0;
for(int i = 1; i <= ptime; i++){
    dp[i] = dp[i - 1] + ttime[i];
}

long long max = dp[atime]; 
int m_stime = 0;
for(int i = 1; i <= ptime - atime; i++){   
    if(max < dp[i + atime] - dp[i]){
        max = dp[i + atime] - dp[i]; m_stime = i;
    }
}
```

위와 같이 dp 배열을 만들고, 특정 구간에서 광고를 삽입하기 적당한 구간을 구하면 된다.


<br/><br/>

#### 세번째 오류 : long long

logs가 30만개니까, 최악의 경우 모든 time 배열에 30만이라는 숫자가 들어갈 수 있으며, dp가 30만이 몇번씩 더해질 수 있어서 수가 매우 커질 수 있음. 모든 배열을 long long으로 바꿔서 연산해야할 것.



</span>