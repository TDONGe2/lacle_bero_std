# I/O 와 디스크 관계

# 2.오라클과 디스크 (HDD)

---

DBMS 의 데이터 흐름은 디스크에 저장하기, 디스크에서 꺼내오기 때문에 서로 떼려야 땔수 없다.

다양한 보조기억 장치의 종류 가 있지만 (자기테이프, SSD, HDD…) 이책에선 HDD 기준으로 설명

![하드디스크 사진](https://prod-files-secure.s3.us-west-2.amazonaws.com/19b7579a-078e-4e49-898c-90f2eb8c4176/d67245f9-ff16-4dbc-853a-defb5bd905f7/Untitled.png)


하드디스크 사진

* 내부에 있는 플래터(platter)의 분당 회전수 1분에 1만번
* 헤드가 트랙의 첫머리 찾는데 1초에 백번가능
  ![하드디스크 플래터 구성](https://prod-files-secure.s3.us-west-2.amazonaws.com/19b7579a-078e-4e49-898c-90f2eb8c4176/67f7abab-b674-409e-8afe-956d7f9f1918/Untitled.png)
  하드디스크 플래터 구성

# 3. 디스크의 동작

---

## 동작방식

원하는 데이터에 접근하는 과정

1. 데이터가 저장시작한 첫머리 찾기 (seek)
2. 원하는 정보의 위치가 회전해서 다가 올 떄 까지 기다리기 (회전 대기시간  rotaional latency time)
   ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/19b7579a-078e-4e49-898c-90f2eb8c4176/f64634d0-ca66-4c00-964a-0ca441a4e49e/Untitled.png)

이 모든 과정은 m (10 ^ -3)단위로 이루어진다.

하지만 CPU 가  메모리에 접근할때 n (10 ^-9) 이기때문에 이 갭차이로 인해 OS 입장에선 디스크 데이터를 접근할때 기다릴수 밖에 없다.

그렇기 때문에 DBMS 는 데이터 I/O 시간을 줄여야한다.

### 어떻게 i/o 대기시간을 줄일까?

io 는 시퀀셜 엑세스, 랜덤 엑세스로 나눌수 있다

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/19b7579a-078e-4e49-898c-90f2eb8c4176/3c54d57b-047c-4bdd-8923-fbe120d11476/Untitled.png)

### 시퀀셜 ( sequential )

* 데이터의 첫부분 부터 끝까지 순서대로 ( sequential ) 다 읽어오는 것
* 메모리에 없다 ( 캐싱 된게 없다) 면 full scan 을 진행 (multi block io)
* 느리다 1GB 면 50 초 100MB 면 5초

### 인덱스(INDEX)

* 시퀀셜 조회 느린 성능을 극복하기 위해 사용
* 데이터의 키값 (id )을  index 에 기록된 rowid(주소) 를 찾아 데이터를 가져옴
* 인덱스는 트리 자료구조 구성
* 인덱스는 크기가 크다고 심하게 느려지지 않는다.

### 렌덤 엑세스

* 실제로 데이터를 조회할땐 연속적으로 데이터를 조회하는 일은 없다.
* 이럴 경우 HDD 헤드가 많이 움직이며 데이터 접근 하게 된다
* 전송속도 가 20MB/sec (20 × 1000 KB/sec) 이라면 데이터 블록 8KB 기준 1초당 100회 접근 가능이라하면 800KB/sec  (8 x 100KB/sec) 약 20 배차이
* 효율성  렌덤 < 시퀀셜

# 4. 데이터 보증을 위한 디스크

---

DBMS 는 commit 한 데이터는 안전하게 저장된걸 보장할수 있어야한다.

* commit 은 디스크에 저장하는 과정이다.
* Commit 이 많이 진행되면 느려질거 같은데? → 이를 보완하기 위한 장치가 있다.
* 로그를 먼저기록 ( redo ) 하고 disk 에 저장
* Disk 에 저장할땐 비동기로 저장 check point

# 5. 티베로와 디스크

---

### tbiobench

우리가 설치후 수행하는 disk i/o 측정

```bash
tbiobench  -i all -s 100M -F O_SYNC -F O_DIRECT -k
```

### 디스크 관련 이슈

[TmaxSoft IMS System [ims-02]](https://ims.tmaxsoft.com/tody/ims/issue/issueView.do?issueId=307823)

# 결국  여기서 말하고 싶은건?

DBMS 의 성능은 결국 디스크의 성능이 중요하다.

인덱스, 파티션, 병렬처리, 캐싱 은 디스크 성능이 느림을 극복하기 위해 만들어진거다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/19b7579a-078e-4e49-898c-90f2eb8c4176/cfbc48a5-86e5-4864-b16e-27218ddb635f/Untitled.png)

# 참조
