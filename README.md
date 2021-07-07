# docker

 **.https://www.youtube.com/channel/UCW_PO0316aD16L3IcD34wPg 강의 내용 정리**

## 도커 이전
> 기존의 가상머신은 하드웨어를 추상화 시키기 때문에 기존 os 위에 하이퍼바이저를 설치해 가상하드웨어를 만들고 그 위에 os를 올리는 방식이다. 그렇다 보니 부하가 심하고 불필요한 리소스가 많이 들어간다. 그래서 이런 하이퍼바이저와 OS를 뺄수 없을까? 하는 생각에서 docker가 탄생했고 컨테이너가 저 역할을 해준다
<img src="https://www.zdnet.com/a/hub/i/r/2014/10/04/a718b5bd-4bd3-11e4-b6a0-d4ae52e95e57/resize/770xauto/03d3ed9daa6c2b99b94c1cdf77a67737/docker-vm-container.png"/>

* 위의 그림이 vmware와 docker의 구성비교 그림이다.
* vmware는 실제 os역할을 해야 하기 때문에 guest Os가 굉장히 크게 들어가고 그 밑에 하이퍼바이저(없는 CPU,없는 RAM, 없는HDD을 만들어주는 도구)가 있어 매우 무겁고 느리다
* 반면 docker는 docker 엔진이라는 것이 os위에서 동작하는데 컨테이너라는 것을 생성할수 있다. 이렇게 하면 도커 이미지에 서버운영을 위한 프로그램과 라이브러리만 격리해 설치할수있고 os자원은 호스트와 공유할수 있다. 또한 하드웨서 추상화 계층(하이퍼바이저)가 없기때문에 메모리접근,네트워크속도등이 훨씬 가상머신보다 빠르다.
* LXC : 기존 리눅스에서 chroot라는 명령어로 루트디렉토리를 특정디렉토리로 바꿔주면 특정디렉토리는 chroot jail이라는 환경이 생성되고 해당 디렉토리는 외부의 디렉터리와 격리된 공간이 된다.chroot jail은 파일 시스템에 접근과 디렉토리, 네트워크 및 프로세스등을 컨트롤 할 수 있는데 LXC(LinuxX Countainer)가 jail과 유사한 개념으로 구현 되었다.리눅스 커널레벨에서 제공하는 일종의 격리된 가상공간이고 이곳엔 OS가 설치되지 않기에 **컨테이너**라고 부른다
* 
* cgroup : 리눅스 커널의 cgroup은 OS가 관리하는 다양한 리소스를 중앙에서 제어하기 위한 도구이다. 리소스를 그룹화하고 각 그룹에 대해 우선순위 및 사용가능한 리소스를 제한하거나 그룹을 분리하는 등의 기능을 제공한다.cgroup에서 관리하는 대상은 파일 시스템이나 프로세스뿐만 아니라 CPU 리소스와 메모리, 각종 디바이스, 네트워크 패킷,네트워크 인터페이스 등 이다. 또한 CPU, 메모리, 디스크, 네트워크 자원등을 할당하여 완전한 형태의 가상공간을 제공한다. 또 프로세스트리,사용자 계정 파일시스템등을 격리시켜 호스트와 별개의 공간을만들고 이것을 **namespages** 라고 한다
=> LXC는 Cgroup과 namespace를 결합하여 애플리케이션을 위한 고립 환경을 제공한다. Docker또한 LXC를 실행드라이버의 하나로 사용할수 있으며 이를 통해 이미지 관리와 개발서비스를 제공한다. 현재는 독자적 컨테이너 드라이버를 개발해 사용하고 있다.

## 1. 도커란 ?  
* 컨테이너 기술을 지원하는 다양한 프로젝트중에 하나이다
* 컨테이너 기술을 이전에도 있었으나 도커로 인해 알려졌다, 컨테이너 기술의 사실상 표준
* 다양한 운영체제에서 사용가능
* 애플리케이션에 국한되지 않고 의존성 및 파일 시스템까지 패키징하여 빌드,배포,실행을 단순화 시킴
* 리눅스의 네임슾이스와 cgroups와 같은 커널 기능을 사용하여 가상화
<img src="https://postfiles.pstatic.net/MjAyMDAzMDZfMTE4/MDAxNTgzNDc1MDc0ODMx.rzo98o-b74RLfMYyGmXQ9CEYH-C65okaZo2UvzgXDmgg.R_6l3KbJgnghtBnoCJ6dk6rDYwOD3dFICXC2ylPlBt4g.PNG.isc0304/Untitled_1.png?type=w773"/>

* 도커는 다양한 클라우드 서비스 모델과 같이 사용 가능
 * 이미지 : 필요한 프로그램과 라이브러리, 소스를 설치한뒤만든 하나의 파일
 * 컨테이너 : 이미지를 격리하여 독립된 공간에서 실행한 가상 환경
<img src="https://postfiles.pstatic.net/MjAyMDAzMDZfNDcg/MDAxNTgzNDc1MTA5MzU4.yspmTYx1_6GitcIO9C4BmuceQ-hWa77OAAdjOkL5u34g.SZq2CNDfP_vW7bQ_BtUqydpDrExwgLmEXAL22gM2GoAg.PNG.isc0304/Untitled_2.png?type=w773" />

## 2. 도커의 컨테이너 기법
* 
