# CPU 스케줄링

## **CPU 스케줄링 개요**

### **CPU 스케줄링의 필요성**

- 다중 프로그래밍의 목적은 CPU 이용률 최대화 => 가능한 CPU가 항상 실행중인 프로세스를 가지게 한다.
- 프로세스가 대기 중일 때, 운영체제가 해당 프로세스가 할당된 CPU를 회수해 다른 프로세스에 할당

### **CPU-입출력 버스트 사이클(CPU-I/O Burst Cycle)**

**버스트(burst)**란 특정 기준에 따라 한 단위로서 취급되는 연속된 신호나 데이터의 모임을 말한다.

즉, 입출력 요청을 위해 CPU 사용을 사용했다가 쉬었다가를 반복한다.

프로세스가 CPU를 사용할때를 **CPU버스트**, 입/출력을 기다릴때를 **입/출력 버스트**라고 한다.

프로세스의 실행은 CPU 버스트를 시작으로 뒤이어 입출력 버스트가 발생하는 식으로 두 버스트의 사이클로 구성된다.

<img src="CPU_스케줄링.assets/123.jpg" alt="123.JPG" style="zoom:67%;" />

마지막 CPU버스트는 또 다른 입출력 버스트가 뒤따르는 대신에 실행을 종료하기 위한 시스템 요청과 함께 끝난다.

입출력 중심의 프로그램은 CPU 버스트 시간이 짧을 것이다.

반대로 CPU 지향 프로그램은 CPU 버스트 시간이 길 것이다.

이러한 분포는 적절한 CPU 스케줄링 알고리즘을 선택하는데 매우 중요할 수 있다.

### **스케줄러&디스패쳐**

### **CPU 스케줄러**

- 장기스케줄러 : 한정된 메모리 공간에 프로세스들이 한번에 올라온 경우, 디스크에 임시로 저장된다. 이 프로세스들 중 어떤 프로세스에 메모리를 할당하여 ready queue로 보낼지 결정하는 역할을 한다.
- 일반적으로 ready queue에 저장된 레코드는 PCB
  
    *PCB : 운영체제가 프로세스를 제어하기 위해 정보를 저장해 놓는 곳으로 프로세스의 상태 정보를 저장                   하는 구조체
    
- 중기스케줄러 : 현 시스템에서 메모리에 너무 많은 프로그램이 동시에 올라가는 것을 조절하는 스케줄러
- 단기스케줄러 : CPU와 메모리 사이의 스케줄링 담당, ready queue에 존재하는 프로세스 중 어떤 프로세스를 run 시킬지 결정

### **디스패처(Dispatcher)**

- CPU 코어의 제어를 CPU 스케줄러가 선택한 프로세스에 건내주는 모듈
- 문맥 교환, 사용자 모드 전환, 프로그램을 다시 시작할 때 프로그램의 적절한 위치로 이동하는 일
- 디스패처가 하나의 프로세스를 정지하고 다른 프로세스의 수행을 시작하는데 소요되는 시간 => 디스패처 지연
- 문맥 교환 시 항상 호출되기 때문에, 문맥 교환의 오버헤드를 줄이기 위해 디스패처의 시간 효율은 매우 중요하다.

### **CPU 스케줄링이 발생하는 상황**

- 프로세스가 실행 상태(run)에서 대기 상태(waiting)로 전환될 때

ex) I/O 요청, 자식 프로세스의 종료를 기다리기 위해 wait() 호출 시

- 프로세스가 실행 상태(run)에서 준비 상태(ready)로 전환될 때

ex) 인터럽트 발생

- 프로세스가 대기 상태(waiting)에서 준비 상태(ready)로 전환될 때

ex) I/O 종료

- 프로세스가 종료될 때

*1, 4번은 비선점(nonpreemtive) 또는 협조적(cooperative)스케줄링이라고 한다.

CPU가 한 프로세스에 할당되면 프로세스가 종료되거나 대기 상태로 전환되어 CPU가 방출될때까지 점유한다

*2, 3번은 선점(preemtive)스케줄링이라고 한다

CPU가 한 프로세스에서 작업이 안끝나더라도 인터럽트가 발생하면 다른 프로세스의 작업을 함 

최신 운영체제는 선점 스케줄링을 사용한다

### **스케줄링 기준 (스케줄링 알고리즘을 비교하는 기준)**

- CPU 이용률(utilization): CPU를 얼마나 사용하고 있는가
- 처리량(throughput): 단위 시간 당 완료된 프로세스의 개수
- 총처리 시간(turnaround time):

프로세스의 제출 시간과 완료 시간의 간격 (프로세스의 대기 시간(ready queue에서의) + CPU 실행 시간 + I/O 시간)

- 대기 시간(waiting time): 프로세스의 대기 시간(ready queue에서의)
- 응답 시간(response time): 요구한 후, 첫 응답이 나올 때 까지의 시간 (응답을 모두 처리할 때가 아니다.)

=> CPU 이용률, 처리량을 최대화하고 총처리 시간, 대기 시간, 응답 시간을 최소화하는 것이 바람직하다.

# CPU 스케줄링 알고리즘 1

> *****스케줄링 알고리즘 (Scheduling Algorithms)*****
> 
> 
> ### ***선입 선출 스케줄링 (FCFS, First-Come First-Served)***
> 

<img src="CPU_스케줄링.assets/Untitled.png" alt="Untitled" style="zoom:50%;" />

- 먼저 온 순서대로 처리하는 방식 (`nonpreemptive`) = (비선점)
- CPU를 오래 쓰는 프로세스가 먼저 와서 CPU를 할당 받으면, 나머지 프로세스들은 전부 기다려야하므로 효율적이지 않음

![https://blog.kakaocdn.net/dn/bQz2Y5/btrns3gYrw7/MDBIeTLGjaPtWIsX2q6zM0/img.png](https://blog.kakaocdn.net/dn/bQz2Y5/btrns3gYrw7/MDBIeTLGjaPtWIsX2q6zM0/img.png)

- 위 사진처럼 어떤 프로세스가 먼저 실행되느냐에 따라 전체 대기 시간에 상당한 영향을 미침
    - 1번
        - 대기 시간: P1 = 0, P2 = 24, P3 = 27
        - 평균 대기 시간: (0 + 24 + 27) / 3 = 17
    - 2번
        - 대기 시간: P1 = 6, P2 = 0, P3 = 3
        - 평균 대기 시간: (6 + 0 + 3) / 3 = 3
- 긴 프로세스 하나 때문에 짧은 프로세스 여러 개가 기다리는 현상을 `Convoy effect` 라 부름

### ***최단 작업 우선 스케줄링 (SJF, Shortest-Job-First)***

<img src="CPU_스케줄링.assets/Untitled%201.png" alt="Untitled" style="zoom:50%;" />

- SJF는 CPU 버스트가 가장 짧은 프로세스에게 제일 먼저 CPU를 할당하는 방식
- 평균 대기 시간을 가장 짧게 하는 최적 알고리즘
- 두 가지 방식
    - nonpreemptive (비선점형)
        - 일단 CPU를 잡으면 더 짧은 프로세스가 들어와도 CPU 버스트가 완료될 때까지 CPU를 선점당하지 않음.
    - preemptive (선점형)
        - SRTF (Shortest-Remaining-Time-First)
        - CPU를 잡았다 하더라도 더 짧은 프로세스가 들어오면 CPU를 뺴앗김.
- 문제점
    - Starvation (기아): 짧은 프로세스로 인해 긴 프로세스가 영원히 CPU를 잡지 못할 수 있음
    - CPU 버스트 시간을 미리 알 수 없음: 과거 CPU 사용 시간을 통해 추정만 가능

**비선점형 방식 예시**

![Untitled](CPU_스케줄링.assets/Untitled%202.png)

**선점형 방식 예시**

![Untitled](CPU_스케줄링.assets/Untitled%203.png)

### ***우선 순위 스케줄링 (Priority Scheduling)***

<img src="CPU_스케줄링.assets/Untitled%204.png" alt="Untitled" style="zoom:50%;" />

- 우선 순위가 제일 높은 프로세스에게 CPU를 할당
- 일반적으로 우선 순위 값 (priority number)가 작을 수록 높은 우선 순위를 갖는다.
- 두 가지 방식
    - nonpreemptive (비선점형)
        - 일단 CPU를 잡으면 더 높은 우선 순위를 가진 프로세스가 들어와도 CPU 버스트가 완료될 떄까지 CPU를 선점당하지 않음.
    - preemptive (선점형)
        - CPU를 잡았다 하더라도 더 높은 우선 순위를 가진 프로세스가 들어오면 CPU를 빼앗김
- SJF는 일종의 우선 순위 스케줄링이라고 볼 수 있음 (우선 순위 = 예상되는 다음 CPU 버스트 시간)
- 문제점
    - Starvation (기아): 우선 순위가 낮은 프로세스는 영원히 CPU를 잡지 못할 수 있음
- 해결 방안
    - Aging (노화): 아무리 우선 순위가 낮은 프로세스라 하더라도 시간이 오래 지나면 우선 순위를 높여주는 기법.

### ***라운드 로빈 스케줄링 (RR, Round Robin)***

![Untitled](CPU_스케줄링.assets/Untitled%205.png)

![Untitled](CPU_스케줄링.assets/Untitled%206.png)

- 각 프로세스는 동일한 크기의 할당 시간인 `time quantum` 을 가짐
- 할당 시간이 지나면 프로세스는 CPU를 빼앗기고 Ready Queue 맨 뒤에 가서 줄을 서게 됨
- 짧은 응답 시간을 보장함
    - 조금씩 CPU를 줬다 뺏었다를 반복하기 때문에 CPU를 최초로 얻기까지 걸리는 시간이 짧음
    - n개의 프로세스가 Ready Queue에 있고, 할당 시간이 q time unit인 경우 어떤 프로세스도 `(n - 1) * q time unit` 이상 기다리지 않음
    - CPU를 할당받기 위해 기다리는 시간이 CPU 버스트에 비례
- 성능
    - q가 커질 수록 FCFS에 가까워짐
    - q가 작을 수록 context switch 오버헤드가 증가함
- 일반적으로 SJF보다 평균 turnaround time (소요 시간)이 길지만, 응답 시간은 짧음
- 시간이 오래 걸리는 job과 짧게 걸리는 job이 섞여 있을 때는 효율적이지만, 모든 시간이 동일한 job만 있을 때는 비효율적이다.

![Untitled](CPU_스케줄링.assets/Untitled%207.png)

위와 같이 라운드 로빈은 일정 할당 시간(20)을 공평하게 할당 받아서 CPU 사용이 가능하다.

# CPU 스케줄링 알고리즘 2

앞선 스케줄링들은 **실행 시간을 측정**할 필요성이 있는데,

이는 부하가 걸리는 작업이며 정확성도 떨어집니다.

따라서 이를 측정하지 않아도 여전히 상호작용 프로세스(IO bounded job)가 높은 우선순위를 갖고 빠른 응답시간을 주기 위해 고안된 방법들을 추가로 소개합니다.

## **Multilevel Queue**

- 작업별로 별도의 ready Queue를 여러 개로 만들어 분할
- 각각의 큐는 독립적인 스케줄링 알고리즘을 가짐
    - foreground(interactive) - RR
    - background(batch - no human interaction) - FCFS
- 여러 개의 큐를 관리하는 스케줄링이 필요 → overhead
    - **Fixed priority scheduling -** foreground에게 무조건적으로 우선순위를 주는 것. foreground가 비었을때 background를 실행합니다. 이를 통해 상호작용 프로세스를 빠르게 처리할 수 있지만, background에 대한 starvation이 발생.
    - **Time slice** - 각 큐마다 시간 비율을 주는 방법. foreground에게는 전체 CPU 시간의 80%을 분배, background에게 20%을 분배해 starvation을 방지하면서도 foreground job들이 더 빨리 CPU를 얻을 수 있습니다.
- 최초로 배정된 큐에서 벗어나지 못함
  
    <img src="CPU_스케줄링.assets/Untitled%208.png" alt="Untitled" style="zoom:50%;" />

## Multilevel Feedback Queue

- MLQ에서 프로세스의 Queue간의 이동이 허용된 형태
- 선점(Preemtive) 스케줄링
- Feedback을 통해 우선 순위를 조정
    - 현재까지 프로세서를 사용한 정보(패턴)을 활용 → 에이징 aging
    - 우선순위가 동적으로 변합니다.

<img src="CPU_스케줄링.assets/Untitled%209.png" alt="Untitled" style="zoom:50%;" />

<img src="CPU_스케줄링.assets/Untitled%2010.png" alt="Untitled" style="zoom:50%;" />

<img src="CPU_스케줄링.assets/Untitled%2011.png" alt="Untitled" style="zoom:50%;" />

멀티 레벨 피드백 큐를 구현하는 하나의 예시

- 처음 들어올 땐 맨 위에서 들어오고
- 각각의 큐는 RR 시간8, RR 시간16, FCFS 쓰고
- 상위 큐로 승격이 일어나진 않음
- 할당 시간을 다 쓰고 나면 하위 큐로 강등
- 위쪽 큐가 우선순위 높아 위쪽 큐가 빌때만 아래쪽 큐 실행
  
    → Starvation 문제 발생
    

**단점**

- 설계 및 구현이 복잡하고 스케줄링 overhead가 크다
- Starvation 문제가 발생한다. →time slice

**단점을 보완하기 위한 변형**

<img src="CPU_스케줄링.assets/Untitled%2012.png" alt="Untitled" style="zoom:50%;" />

일반적으로는 멀티 레벨 피드백 큐가 큐를 여러개 두고 피드백 받아서 여러 큐를 옮겨갈 수 있도록 스케줄링 알고리즘을 만듭니다.

## 에이징 기법

<img src="CPU_스케줄링.assets/Untitled%2013.png" alt="Untitled" style="zoom:50%;" />

## Multiple-Processor Scheduling

[https://velog.io/@pu1etproof/나만의-백과사전-멀티-프로세싱-스케줄링](https://velog.io/@pu1etproof/%EB%82%98%EB%A7%8C%EC%9D%98-%EB%B0%B1%EA%B3%BC%EC%82%AC%EC%A0%84-%EB%A9%80%ED%8B%B0-%ED%94%84%EB%A1%9C%EC%84%B8%EC%8B%B1-%EC%8A%A4%EC%BC%80%EC%A4%84%EB%A7%81)

---

싱글 프로세서 시스템에서 멀티프로세서 시스템으로 갈 때 가장 중요한 것은 **하드웨어 캐시의 사용과 CPU간의 데이터 공유**입니다.

<img src="CPU_스케줄링.assets/Untitled%2014.png" alt="Untitled" style="zoom: 33%;" />

### 싱글 프로세서

하나의 캐시 메모리가 존재합니다. 이는 메인 메모리에 있는 현재 프로세스에서 자주 사용되는 데이터들이 저장되어있고, 이를 재사용 시 캐시의 빠른 속도로 인해 느린 메모리를 빠른 것처럼 보이게 할 수 있습니다.

**캐시의 지역성 locality**

**시간 지역성 temporal locality**

최근에 사용한 데이터는 곧 다시 사용될 가능성이 높다

**공간 지역성 Sptial locality**

어떤 데이터가 저장되었다면, 그 근처에 존재하는 데이터들도 사용될 가능성이 높다

→ 이러한 기준으로 캐시에 저장될 내용들을 선정합니다.

<img src="CPU_스케줄링.assets/Untitled%2015.png" alt="Untitled" style="zoom:50%;" />

### 멀티 프로세서

여러 CPU가 하나의 메모리를 공유한다면 개별 캐시 메모리들을 갖게 됩니다. 메인 메모리의 수정은 매우 느려 CPU는 쓰기 작업 수행 시 메인 메모리 값 변경 대신 캐시 값만 수정하고 메모리를 나중에 업데이트 하는데(delayed write), 도중에 다른 프로세서가 메인 메모리 값을 읽으면 동시성 문제 발생하므로 lock 등을 걸어 주의해서 짜야 합니다.

→ 기본적인 방법은 HW 선에서 제공됩니다. 메모리 접근을 모니터링 하는 것입니다.

→ 각 캐시는 메인 메모리에 연결한 버스를 관찰하고, 메모리 업데이트를 관찰합니다.

→ 그 뒤에 CPU가 캐시 데이터 수정 시 이를 인식해 나중에 메모리가 캐시에서 수정된 데이터를 

가져올 때 적절히 처리할 수 있도록 합니다.

### 동기화

여러 CPU에서 공유 데이터에 접근할 때에는 상호배제 lock 등을 사용해 정확성을 보장해줘야 합니다. 싱글 큐로 스케줄링 하는 경우 lock사용 시 실제 프로그램 수행 시간보다 lock 처리에 더 많은 시간을 사용하게 될 수도 있습니다.

### 캐시 선호도 Cache Affinity

특정 CPU에서 프로그램을 수행하면 해당 프로그램은 CPU의 캐시에 여러 데이터를 저장합니다. 다음에 다시 수행될 때 캐시에 저장된 정보를 가지고 더 빨리 수행하는게 퍼포먼스가 좋으니까!

그런데 CPU가 여러개인 환경에서는 수행되다가 다른 CPU로 넘어가면 또다시 필요한 데이터를 가지고 와야하는 문제가 발생합니다. 즉, 성능이 저하됩니다.

따라서, 멀티 프로세서 스케줄러는 프로그램을 스케줄링할 때 캐시 선호도를 고려해 프로그램을 동일한 CPU에서 계속 실행되도록 스케줄 해줘야 합니다.

<img src="CPU_스케줄링.assets/Untitled%2016.png" alt="Untitled" style="zoom:33%;" />

캐시 선호도를 전혀 고려하지 않은 싱글 큐 스케줄링(SQMS)

SQMS는 이를 해결하기 위해 별도의 매커니즘을 사용해 캐시 선호도 구현

<img src="CPU_스케줄링.assets/Untitled%2017.png" alt="Untitled" style="zoom:50%;" />

E는 캐시 선호도가 고려되지 않습니다. SQMS 상에서는 캐시 선호도가 적용되지 않는 프로세스가 존재할 수도 있습니다.

모든 CPU가 하나의 큐에서 작업을 가져오는 경우 작업이 여러개의 CPU를 오가기 때문에 성능이 나빠지므로 이를 보존하기 위한 알고리즘이 대부분 포함됩니다.

## 멀티 큐 스케줄링 MQMS

싱글 큐의 문제를 보완하기 위해 여러 줄의 큐를 사용합니다. 큐가 여러개이며 CPU는 각각 하나 이상을 갖고 각각의 큐는 RR과 같은 특정한 스케줄링 방법을 이용합니다.

작업이 시스템에 들어오면 heuristic에 딸 ㅏ하나의 큐에만 배치되고, 그 뒤엔 CPU가 독립적으로 스케줄링 되므로 SQMS에서 발생하는 문제를 방지할 수 있습니다.

![Untitled](CPU_스케줄링.assets/Untitled%2018.png)

개별 큐를 가지면 CPU는 자신이 처리할 큐에 있는 작업들만 처리하므로 캐시 친화도 적용 잘 됨

하지만…

<img src="CPU_스케줄링.assets/Untitled%2019.png" alt="Untitled" style="zoom:50%;" />

프로세스가 편향적으로 배치되면 A가 CPU 독점하게 되며

<img src="CPU_스케줄링.assets/Untitled%2020.png" alt="Untitled" style="zoom:50%;" />

A가 종료되면 이런 형태로 CPU0이 놀게되어 전체 성능이 저하됩니다.= load imbalance

![Untitled](CPU_스케줄링.assets/Untitled%2021.png)

그래서 이 때는 B나 D를 옮겨주고

![Untitled](CPU_스케줄링.assets/Untitled%2022.png)

![Untitled](CPU_스케줄링.assets/Untitled%2023.png)

이럴 때에는 프로세스를 계속 이동시키면 load balance가 지켜집니다.

프로세스 이동 결정 방법은 work stealing이 있습니다. 작업이 적은 큐가 다른 큐를 보고 작업이 많아보이면 작업을 훔쳐옵니다.

→그런데 작업을 자주 훔치면 동일한 문제가 발생하고, 훔치지 않으면 load balance가 자주 발생하므로 이 둘을 잘 조율하는 값을 찾는 것이 중요합니다.

**Homogeneous processor**

- Queue에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 합니다.
- 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우 복잡

**Load sharing 부하 공유**

- 여러 CPU를 사용할 수 있으므로 부하 공유가 가능합니다.
- 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
- 별개의 큐를 두기 vs 공동 큐를 사용하기

### 프로세싱 기법에 따른 분류

### **대칭 멀티 프로세싱 Symmetric Multiprocessing SMP**

- **각 프로세서가 각자 알아서 스케줄링을 결정**
- 대부분의 멀티프로세서가 이러한 형태
- 각각의 프로세서가 동등한 권한으로 프로세싱
- SMP에서 프로세서가 하나 이상(처리기마다 ready queue가 존재)이라는 것을 최대한 활용하려면 부하를 모든 프로세서에 균등하게 배분하는 것이 매우 중요(load balance)
- 프로세스들은 하나의(공동의) ready queue거나, 각 프로세서마다의 private ready queue에 저장되어 있으며, 모든 프로세서에는 각각의 스케줄러가 있어야 한다. 때문에 동시에 여러 프로세서에서 공유자원에 접근할 시 동기화 문제가 발생할 수 있다.

### **Asymmetric multiprocessing**

- 하나의 master 프로세서가 시스템 데이터의 접근과 공유를 책임지고 관리감독하며 나머지 프로세서는 거기에 따름
- master 프로세서가 모든 스케줄링 결정과 입출력 처리, 로드 밸런싱을 다 결정
다른 프로세서들은 User 코드만 수행한다.
- 한 프로세서가 다 처리하기 때문에 공유자원이 존재하지 않고 프로세서 간의 이동이 필요없습니다.

### 메모리 접근에 따른 구분

### Uniform Memory Access

- 하나의 공유된 메모리에 여러개의 프로세서가 할당된 형태
- 하나의 메모리를 공유하여 사용한다.
- 프로세서가 어느 메모리를 참조하든 접근속도가 동일하다.

### Non-Uniform Memory Access(NUMA)

- 각각의 프로세서에 메모리가 따로 담겨있는 형태
- 만약 다른 프로세서에서 자신의 메모리를 참조할 때 속도가 느리므로, 스케줄링 시에 이를 고려해줘야 한다.

<img src="CPU_스케줄링.assets/Untitled%2024.png" alt="Untitled" style="zoom:50%;" />

## Real-Time Scheduling

[https://lazymankook.tistory.com/49](https://lazymankook.tistory.com/49)

주어진 시간 안에 꼭 처리해야하는 데드라인이 있는 job을 real-time job이라 합니다.

즉 **task 처리에 걸리는 시간을 일관되게 유지할 수 있냐가 중요한 성능의 척도입니다.**

Real-time system의 목표는 실시간 성능 보장에 있다.

- Soft real-time systems: task 처리 시간이 달라지면 성능이 감소하는 경우 (streaming video)
    - 소프트 리얼타임 시스템은 경우는 OS나 시스템 상에서 리얼타임 지원을 제대로 안 해주지만 그냥 그 환경 안에서 리얼타임 잡을 수행합니다.(cost의 문제)
    - 따라서 여러 프로세스를 동시에 돌리면 동영상이 끊기는 등의 문제가 발생하기도 합니다.

하지만 그래도 다른 프로세스보단 우선순위를 주는 편입니다.

- Hard real-time systems: task 처리 시간이 달라지면 실패하는 경우 (자동차의 air-bag 등)
    - 하드 리얼타임 시스템의 경우 오프라인으로 미리 스케줄링해서 그대로 시스템이 따라가도록 하는 경움가 많습니다. 즉, CPU 도착 시간을 알고 스케줄링 하는 편입니다.

## Thread Scheduling

[https://www.geeksforgeeks.org/thread-scheduling/](https://www.geeksforgeeks.org/thread-scheduling/)

하나의 프로세스 안에서 CPU 수행 단위가 여러 개 존재할 때.

**Local Scheduling**

User level thread 운영체제는 스레드의 존재를 모르고 사용자 프로세스 본인이 내부에 스레드를 여러개 두는 것. 이 경우에는 운영체제에 있는 스케줄러가 어느 스레드한테 줄지 지목해줄 수 없습니다. 그냥 프로세스한테 권한 주면 프로세스가 내부적으로 어떤 스레드에게 줄지 결정해야 합니다.

**Global Scheduling**

Kernel level thread 운영체제가 스레드의 존재를 아는 것. 이 프로세스는 스레드 여럿으로 구성되어 있음을 알고 스케줄링 시 어느 스레드에게 할당할지 운영체제가 직접 결정하는 방식

**LWP Light Weight Process 경량 프로세스**

[https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=sageleaf&logNo=40002063994](https://m.blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=sageleaf&logNo=40002063994)

사용자 레벨 스레드 (User Level Thread)가 물리적 CPU 리소스에 액세스할 수 있도록 인터페이스 역할을 하는 User interface의 스레드.

**LWP는 커널과 사용자 프로세스를 연결하는 인터페이스 역할을 합니다.**

스레드 라이브러리는 많은 사용자 스레드로 부터 실행될 스레드를 선택하고, 어떤 스레드가 어떤 LWP에서 실행되고, 얼마나 오래 실행될지 예약합니다.

쓰레드의 정체성(우선 순위, PID, UID ...)을 가지고 LWP를 구성한 후, LWP는 다시 커널에 의해 스케쥴링될 수 있는 커널 스레드로 매핑됩니다.

LWP는 다음과 같은 특징을 갖는 커널 자료 구조입니다.

- 여러 개의 LWP는 프로세스 스페이스와 리소스를 공유
- 한 LWP는 수명 주기 동안 다른 CPU에서 실행될 수도 있다.
- LWP는 서로 독립적이다.
- 각 LWP는 CPU에서 독립적으로 실행된다.
- 어플리케이션 쓰레드(사용자 쓰레드)는 LWP로 다중화(multiplexed) 될 수 있다.
- LWP를 가상의 CPU라고 할 수 있다.
- 각 LWP는 커널 쓰레드와 연결된다.
- 페이지 데몬(page daemon)과 같은 시스템 쓰레드는 커널 쓰레드는 가지고 있지만, LWP는 가지지 않는다.

<img src="CPU_스케줄링.assets/Untitled%2025.png" alt="Untitled" style="zoom: 67%;" />

<img src="CPU_스케줄링.assets/Untitled%2026.png" alt="Untitled" style="zoom:50%;" />

1. **프로세스 경합 범위 Process Contention Scope (PCS)** 
   
    **경합은 동일한 프로세스 내의** 스레드 간에 발생 합니다 . 스레드 라이브러리는 사용 가능한 LWP를 통해 리소스에 액세스하도록 우선 순위가 높은 PCS 스레드를 예약합니다(스레드 생성 중 응용 프로그램 개발자가 지정한 우선 순위).
    
1. **시스템 경합 범위 System Contention Scope (SCS)**
   
    **경합은** **시스템의 모든 스레드에서 발생합니다.** 이 경우 모든 SCS 스레드는 스레드 라이브러리에 의해 각 LWP에 연결되고 커널 리소스에 액세스하기 위해 시스템 스케줄러에 의해 예약됩니다.
    
    LINUX 및 UNIX 운영 체제에서 POSIX Pthread 라이브러리는 생성 중 스레드에 대한 경합 범위 유형을 정의  하는 함수 Pthread_attr_setscope 를 제공합니다.
    
- 모든 스레드가 PCS인 경우 컨텍스트 전환, 동기화, 스케줄링이 모두 사용자 공간 내에서 발생합니다. 이것은 시스템 호출을 줄이고 더 나은 성능을 달성합니다.
- PCS는 SCS보다 저렴합니다.
- PCS 스레드는 하나 이상의 사용 가능한 LWP를 공유합니다. 모든 SCS 스레드에 대해 별도의 LWP가 연결됩니다. 모든 시스템 호출에 대해 별도의 KLT가 생성됩니다.
- 생성된 KLT 및 LWP의 수는 생성된 SCS 스레드의 수에 따라 크게 달라집니다. 이것은 스케줄링 및 동기화를 처리하는 커널 복잡성을 증가시킵니다. 따라서 SCS 스레드의 수가 PCS 스레드의 수보다 작아야 한다는 SCS 스레드 생성에 대한 제한이 발생합니다.
- 시스템에 둘 이상의 할당 도메인이 있는 경우 자원의 스케줄링 및 동기화가 더 길어집니다. SCS 스레드가 둘 이상의 할당 도메인의 일부인 경우 문제가 발생하며 시스템은 n개의 인터페이스를 처리해야 합니다.

## Algorithm Evalution

스케줄링 알고리즘 어떤게 좋은지 평가하는 방법.

- CPU 이용률
- 응답 시간
- 처리량

등에서 상대적인 중요성을 정의해야 합니다.

![Untitled](CPU_스케줄링.assets/Untitled%2027.png)

<img src="CPU_스케줄링.assets/Untitled%2028.png" alt="Untitled" style="zoom:50%;" />

**Queueing models**

직접 계산해서 알아내는 방법. 서버에 요청들이 도착하면 큐에 쌓여있다가 서버가 처리를 해서 끝나고 빠져나가고 처리 안 되면 큐에 남아있게 됩니다. 이런 일반적인 상황은 CPU 자체, 네트워크 등 여러 상황에서 발생합니다.

CPU를 쓰고자 하는 요청들이 큐에 도착하는데, 도착률(얼마나 빠른 빈도로 CPU 쓰겠다고 도착하느냐) **확률 분포**로 주어지고, CPU의 처리율(시간당 얼마나 일을 하는가)도 확률 분포로 주어졌을 때 복잡한 수식 계산을 통해 처리량 Throughput 대기시간 Waiting time 이 나오게 됩니다.

이 분포로부터 알고리즘에 대한 평균 처리량, 이용률, 평균 큐 길이, 평균 대기 시간 등을 계산할 수도 있습니다. → 큐잉 네트워크

n(평균 큐 길이) = lambda(도착률) * W(평균 대기 시간) → 시스템이 안정할 때

현재 분석할 수 있는 알고리즘과 분포 부류가 상당히 제한되어 있습니다.

**구현 & 성능 측정**

실제로 구현해서 성능을 측정하는 방법. 리눅스 스케줄링 알고리즘에서 프로세스 A한테 주다 B한테 주다가 하는데, 내가 만든 알고리즘이 기존 리눅스것보다 좋은지 확인하기 위해 오픈소스로 공개된 코드를 내 알고리즘 식으로 바꾼 후 오리지널 리눅스 컴과 내 알고리즘 리눅스 컴을 돌려서 실제로 프로세스 돌린 성능을 측정하는 등의 방식.

**모의 실험**

이게 너무 어렵기에 대안이 되는 방법으로 나온 것이 모의 실험(시뮬레이션). 가상으로 돌려보는 것. 리눅스 알고리즘 내 알고리즘을 주어진 작업(trace)에 대해 가상적으로 돌려보고 그 결과로 스케줄링의 성능을 파악합니다.

특이한 예제 하나로는 판별할 수 없으므로 실제 시뮬레이션 인풋 데이터 trace를 사용합니다.

trace는 신빙성이 있어야 합니다. 즉, 실제 CPU에서 돌아가는 프로그램과 유사하거나 대변이 가능해야 합니다.

상세하게 할수록 좋은 결과를 얻을 수 있지만, 컴퓨터 사용 시간을 더 필요로 하게 됩니다.

**trace**

실제 프로그램을 돌리면서 CPU 요청 패턴을 봤더니 0초~7초 요청하고 30초~ 어쩌구 요청하고 

실제 실행시킨 후 CPU 사용하는 시간대를 뽑아 이를 시뮬레이션 인풋으로 집어넣습니다. 

→ 실제 프로그램에서 뽑은 것이므로 어느정도 신빙성이 있다

→ 직접 만들더라도 실제 돌아가는 것과 유사하게 만들 것

