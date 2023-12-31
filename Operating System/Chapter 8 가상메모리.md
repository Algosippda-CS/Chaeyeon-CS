# Chapter 8

# 8.1 가상 메모리를 위해서는

페이지(page) : 모든 프로그램은 작은 조각들로 나뉜다, 조각의 크기가 모두 같을 때, 한 조각

세그먼트(segment) : 조각의 크기가 서로 다를 때 각 조각

페이지와 세그먼트 : 메모리와 디스크 사이에서 한 번에 전송되는 전송 단위 → 블록(block)

가상 메모리 관리에서, 

페이지로 나눈 경우 : 페이징, 

세그먼트로 나눈 경우 : 세그먼테이션 시스템

주소의 사상(Mapping)

실행 중인 프로그램에서 참조하는 주소가 실제 메모리에 있는 주소와 달라서, 메모리 상의 주소로 변환이 필요할 때 하는 것

참조하는 주소 : 가상 주소(virtual address) / 실제 메모리상 주소 : 실 주소(real address)

**주소의 지정이 컴파일 시에 이루어진다**

: 프로그램이 실행될 때 참조하는 주소가 컴파일될 대 지정된다는 의미

단점 : 참조하는 주소가 바로 실주소 이기 때문에,  프로그램은 항상 메모리의 지정된 곳으로만 적재되어야 한다. 단순하지만 융통성 X

→주소의 사상이 필요없다.

ex) 고정 분할, 절대 로더

재배치

장점 : 메모리에서의 위치를 적재될 때마다 바꿀 수 있어서 융통성, 메모리의 효율적 이용이라는 단점 : 프로그램 전체가 통째로, 연속적으로 적재되어야한다는 전제가 있음

컴파일 시 주소는 프로그램의 첫번 째 줄을 0으로 하고 나머지는 첫 줄 로부터 얼만큼의 위치에 있는지( 위치값 Offset, distance) 값으로지정함

→ 실행 시 참조되는 주소는 프로그램 내에서 어느정도 위치에 있는지 나타내는 상대주소 (Relative Address) 이며, 실 주소는 메모리에 적재될 때 시작 주소의 값에 상대주소 값을 더해주면 된다.

결론

상대주소로부터 실주소로의 변환이 요구되고, 재배치 레지스터 값을 상대주소에 더하기만 하면되는 간단한 방식이지만, 프로그램이 조각나고 이 조각들의 메모리 적재가 연속적이지 않다면 더 이상 쓸모가 없게 된다.

가상 메모리로 관리

프로그램들은 디스크에 조각 난 모양으로 저장 되고, 이주 몇 개 는 메모리에 비연속적으로 다른 프로그램 조각들과 섞여 적재된다 → 다중 프로그래밍의 정도에 포함됨

가상주소들은 프로그램이 실행될 때, 참조하게 될 모든 조각들 중 “ 몇번째 조각의 몇 번째 위치”인지 의 모양으로 바뀐다. 

ex) goto 10 → <3,5> → 사상과정→ 실 메모리 주소

# 8.2 페이징

페이징을 위해서 모든 프로세스들이 같은 크기의 조각들로 나뉘어야 한다

프레임(Frame) : 메모리 역시 페이지와 같은 크기로 나뉘어 있으며, 일련의 번호가 매겨져 있다. 

페이지의 크기는 주소체계를 고하여 2의 승수 배 크기로 잡는 것이 일반적이다

한프로세스의 전체 페이지들은 디스크에 저장되고, 이 중 몇 개가 메모리에 비연속적으로 다른 프로세스 들의 페이지들과 섞여 적재된다.

프로세스의 실행이 진행되는 과정에 따라 디스크와 메모리를 오가며 교체되는 단위가 페이지이며, 사상의 단위가 된다.

페이지 사상 테이블(Map Table) : 운영체제가 가상 주소를 실 주소로 변환하기 위해 프로세스당 하나의 페이지 테이블을 만들어 둔 것 → 해당 프로세스 페이지 개수에 비례

k개의 페이지를 가지는 프로세스의 페이지 테이블은 k 개의 엔트리로 구성

엔트리 구성 정보

존재(Residence) 비트 : 이 페이지가 메모리에 적재되어 있는가를 나타냄, 1 or 0

존재 비트가 1의 경우 적재되어 있는 프레임 번호를,

0의 경우, 이 페이지가 저장되어있는 디스크의 주소를 나타내는 필드가 있다.

가상 주소 : 페이지 번호(p), 페이지 내에서의 위치(d)로 표시됨

페이징의 사상
![KakaoTalk_20231128_171602099_01](https://github.com/Algosippda-CS/Chaeyeon-CS/assets/61193581/7de4b714-c380-4226-af39-c53670fa0da6)


페이지 테이블은 메모리의 커널 영역에 보관되며, 

실행 중인 프로세스의 페이지 **테이블 시작주소**는 **페이지 테이블 기준** **레지스터(Page Table Origin Register)** 에 들어 있다. 

기준 레지스터 값 + p (실제로는 p x 엔트리 크기) 

페이지 테이블에서 페이지 p의 사상 정보를 갖고있는 엔트리를 찾은 후 , 존재 비트를 확인한다.

존재 비트 =1 , p 가 적재된 프레임번호(f)를 알 수 있으므로, 이 번호 값에 페이지 크기를 곱하면 메모리에서 해당 프레임의 시작주소를 얻고, 여기에 d를 더하여 워드의 주소, 즉 실 주소로 접근 하게 된다

존재 비트 =0 , 접근하고자하는 워드를 포함하는 페이지가 메모리에 없으므로 실행을 위해선 디스크 주소로부터 이 페이지를 메모리에 적재해야 할 것이다.

그 다음 이 엔트리의 존재 비트를 1로 바꾸고, 적재된 프레임 번호를 기입한 후 사상을 계속 진행하면 실주소를 얻게 된다.

## 8.2.1 TLB의 사용
![KakaoTalk_20231128_171602099_02](https://github.com/Algosippda-CS/Chaeyeon-CS/assets/61193581/e7a36fa9-0dff-4140-8e84-a3f55700c9cb)

Translation Lookaside Buffer : 고속 캐시의 일종, 주소로 접근되는 일반 메모리와 달리 키 값으로 찾고자 하는 워드를 동시에 접근하는 연관 메모리 → 검색이 빠르지만 비싼 하드웨어

- 페이지 테이블을 메모리에 두고 접근 할 때 느림을 극복하기위해 사용됨, 가격이 비싸므로 페이지테이블의 일부 엔트리만 수용하는 크기로 사용-
- 빈번하게 검색된 엔트리들을 TLB에 넣되, 페이지 번호 p를 키값으로 동시 검색을 하므로, TLB에 저장되는 각 엔트리는 페이지 번호도 함께 표시되어야 한다.
- 가상 주소의 p를 키 값으로 T:LB먼저 검색하며, 이때 페이지 번호를 p로 가지고 있는 에트리가 있는 경우, 그 엔트리에 적혀있는 프레임 번호를 가지고 실 주소에 이르는 과정자체는 페이징과 동일
- 검색되는 엔트리들의 페이지는 모두 메모리에 적재되어있으므로, 존재비트 확인 필요 X
- TLB에서의 검색에 실패하면 메모리에 있는 페이지 테이블로부터 사상 ( 페이징과 절차 동일)
- 위 에서 얻은 실주소 엔트리는 TLB에 적재시킴 → TLB가 꽉 차 있을 때 교체는 다음장에서
- TLB의 사용으로 실주소 접근 시간이 빨라지기 위해, 원하는 엔트리가 TLB에서 발견되는 확률을 높여야 한다. 이때, T:B 검색 성공 확률을 적중률(hit ratio) 이라 하는데, 소량의 크기라도 90% 이상 나온다는 실험 결과가 있다

## 8.2.2 페이지의 보호와 공유

접근 하고자 하는 페이지에 대한 읽기와 쓰기 작업을 어덯게 제한할 것인가,

다른 프로세스의 주소 공간으로 침범하지 못하도록 하는 것이 보호의 영역

- 프로세스의 메모리 접근은 사상을 거쳐야 하므로 페이지 테이블 각 엔트리에 해당 페이지에 대한 ‘ 보호비트(protection Bits)를 두어 허용되는 접근은 설정
- 쓰기 작업에 대한 보호 비트 =1 ,해당 페이지 쓰기작업이 허용
- 쓰기 작업에 대한 보호 비트 = 0,  보호에 위반되므로 쓰기 시도는 트랩을 일으킴
- 해당 페이지가 자리한 ‘프레임 공간’ 이 해당 프로세스의 주소 공간이며, 각 프로세스의 주소 공간은 서로 침범 해서는 안된다.
    
    ⇒ 따라서 페이징에서의 주소공간 보호는 offset의 크기가 페이지의 크기를 넘지 않으면 안전
    

ex) 다수의 사용자가 한 부의 응용 프로그램을 공유하여 실행한다는 것:

 공유 프로그램 내에서 각자의 실행 위치가 다른 한편, 사용되고 만들어지는 각자의 데이터는 자신들의 주소 공간에 가지도록 한다는 뜻

이때 공유되는 프로그램은 코드의 내용이 실행 도중 변하면 안되므로, 재진입 코드로 컴파일 되어있음.


![KakaoTalk_20231128_171602099_03](https://github.com/Algosippda-CS/Chaeyeon-CS/assets/61193581/5dc2af3a-51a0-47b6-bc0a-aff8c88c2a01)

페이징에서의 공유

- 프로세스 각자의 페이지 테이블에서 엔트리에 같은 프레임번호를 가지도록 함으로써 쉽게 구현
- 페이지는  프로그램(명령어 코드)이 들어있는 ‘코드 페이지’ 와 데이터가 들어 있는 ‘데이터 페이지’로 나뉜다 → 코드페이지는 명령어를 가지므로 가상주소를 참조하는 반면 데이터 페이지는 그럴일이 없다
    
    앞서 말한 대로 코드 페이지 공유를 위해서는 재진입 코드여야 하고, 공유된 데이터 페이지에 대한 쓰기는 상호배제의 해결을 전재해야한다.
    

## 8.2.3 페이징에서 사상 테이블의 구성

TLB가 시간 절약이라면, 사상 테이블은 공간 절약이다.

- 32비트를 사용해 주소를 표현하는 시스템에서 하위 12 비트를 offset으로 사용하면, 페이지 크기는 4Kbyte(2^12)가 되고 사상 테이블은 최대 100만개(2^20)의 엔트리를 가질 수 있다
- 엔트리의 크기를 4바이트로 잡더라도 페이지 테이블의 크기는 4메가바이트가 되므로 매우커서, 메모리에 모두 저장하기엔 벅차므로, 페이지 테이블을 작게 나누어 필요한 부분만 메모리에 적재하기 위해 계층 구조를 갖도록 구성한다

2단계 페이징

- ex) 페이지 번호를 나타내는 20비트를 10비트씩 나누어, 상위10은 바깥(루트) 페이지 테이블 엔트리 위치로, 하위 10 비트는 나누어진 테이블 내에 엔트리 위치를 나타내면 전부 적재해야하는 부담을 줄일 수 있다.

![KakaoTalk_20231128_171602099_04](https://github.com/Algosippda-CS/Chaeyeon-CS/assets/61193581/4da3d478-3151-4c10-bfd7-06d6732dab56)

2단계 페이징의 사상

- 1024 개의 엔트리를 가지는 루트 테이블
- 1024 개의 엔트리를 가지는 페이지 테이블(이  1024개 있다고 상상)
- 루트 테이블의 시작 주소는 기준레지스터에 있다
- 루트 테이블의 각 엔트리는 해당 페이지 테이블의 시작주소를 가지도록 구성된다.

![KakaoTalk_20231128_171602099_05](https://github.com/Algosippda-CS/Chaeyeon-CS/assets/61193581/64167ca4-34d8-49a0-8e3e-7e837402ae7a)

역 페이지 테이블의 사상

- 고정 크기의 페이지 테이블이 한개만 존재
- 메모리의 프레임 수만큼 엔트리를 가짐
- 테이블 내에서 엔트리의 순서 = 프레임의 순서
    
    즉, k번째 엔트리 = k번째 프레임에 대한 사상정보를 가짐
    
- 빈프레임인지를 나타내는 존재 비트와 함께 적재된 경우 어떤 프로세스의 몇번 째 페이지인지를 나타낸다
- 역페이지 테이블에서의 가상 주소 : 페이지 번호 p, 위치값 d, 해당 페이지를 소유하는 프로세스번호 pid
    
    → pid가 있는 이유는, 사상 시 프로세스 번호와 페이지 번호를 함께 사용하여 페이지 테이블을 검색해야 하기때문
    
- 다른 프로세스라 할지라도 페이지 테이블은 같은 페이지 번호로 매겨진다.
- 역페이지 테이블을 사용하면 , 프레임마다 적재한 프로세스 번호를 가져야 하므로, 서로 다른 프로세스번호를 필요로 하는 ‘공유’는 구현이 어렵다.

장점: 페이징은 고정 크기의 페이지로 메모리를 관리함으로써 구현이 쉽기 때문에 대부분의 시스템에서 채택한다

단점: 프로그램의 마지막 페이지는 내부 단편화가 있게된다

⇒ 프로그램이 가지는 논리적인 크기대로 반영되지 못하고, 페이지단위로 억지로 나누면 문제 발생 → 세그먼트 이용 

# 8.3 세그멘테이션


![KakaoTalk_20231128_171602099_06](https://github.com/Algosippda-CS/Chaeyeon-CS/assets/61193581/e1016206-5bae-419d-ae94-04fa9e2119a9)

- 크기가 다름 으로 인해 페이징보다 구현이 복잡하지만, 논리적 단위대로 반영되므로 여러 이점이 있다
- 가상 주소 : 세그먼트 번호 (s) , 세그먼트 내 위치 값(d) ,
- 세그먼트 엔트리 정보 :
- 존재비트 , 존재 비트 값에 따라 유효한 디스크 주소, 메모리 주소가 있는데, 이 값들은 실주소
    
    계산 필요 X
    
- 세그먼트 길이 (length) : 세그먼트 크기가 서로다름으로 인한 조치를 위해 사용
- 접근 제어 키 : 읽기 쓰기 실행 추가 작업에 대해 비트를 0or 1로 지정하여 제어한다

## 8.3.1 세그먼트의 보호와 공유

장점 : 

- 공유: 공유 방법은 페이징과 동일 (엔트리에서 같은 주소 값을 가지게 함)
    
    공유해야 할 프로시저가 커서 몇 개의 페이지로 나누어진다면, 이 페이지들의 엔트리는 공유하는 프로세스들의 페이지 테이블에서 모두 같은 위치에 있어야하고 이것이 테이블의 구성을 어렵게 만들 것이다. / 페이지 크기와 정확하게 맞지 않을 경우 , 공유할 필요없는 부분이 공유 페이지에 포함 될 수도 있다.
    
- 논리적 단위는 단위 별로 수정하고 다시 컴파일 해서 쓸수 있다는 뜻이다. 프로그램 일부의 변경 때문에 전부를 다시 링크하고 로딩하지 않아도 된다!

# 8.4 페이징을 사용하는 세그먼테이션
![KakaoTalk_20231128_171602099_07](https://github.com/Algosippda-CS/Chaeyeon-CS/assets/61193581/ec2cc3f4-76fb-4482-adea-a5a7cf6b630f)
