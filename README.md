# docker

 **.https://www.youtube.com/channel/UCW_PO0316aD16L3IcD34wPg 강의 내용 정리**

## 클라우드란 무엇인가?
> 클라우드 서비스는 2006년 아마존이 처음 서비스를 시작하면서 시작되었다. 아마존은 블랙프라이데이와 사이버먼데이에 다른 시기보다 서버사용량이 엄청 높아지고 이차이가 80% 이상 나게된다. 이땐 서버를 20대만 쓰면 되고 블프땐 80~100대를 사용해야 하는 상황이 었다. 그렇다면 나머지 일반 시기들엔 서버가 노는 상황이 발생했고 놀게 할바에 다른사람들에게 빌려주자 라는 취지로 시작되었다고 한다.
* 클라우드는 기존 기업에서 서비스하려면 많은 하드웨어(서버장비, 라우터,스위치등)와 그에따른 인적자원들(서버 관리자, 보안관리자등)이 필요하지만 클라우드를 사용하면 앞의 대부분을 해결해주는 서비스기 때문에  급부상하게되어 향후 대체불가한 파트가 될것으로 보인다.

## 도커 이전
### 기능
> 기존의 가상머신은 하드웨어를 추상화 시키기 때문에 기존 os 위에 하이퍼바이저를 설치해 가상하드웨어를 만들고 그 위에 os를 올리는 방식이다. 그렇다 보니 부하가 심하고 불필요한 리소스가 많이 들어간다. 그래서 이런 하이퍼바이저와 OS를 뺄수 없을까? 하는 생각에서 docker가 탄생했고 컨테이너가 저 역할을 해준다
<img src="https://www.zdnet.com/a/hub/i/r/2014/10/04/a718b5bd-4bd3-11e4-b6a0-d4ae52e95e57/resize/770xauto/03d3ed9daa6c2b99b94c1cdf77a67737/docker-vm-container.png"/>

* 위의 그림이 vmware와 docker의 구성비교 그림이다.
* vmware는 실제 os역할을 해야 하기 때문에 guest Os가 굉장히 크게 들어가고 그 밑에 하이퍼바이저(없는 CPU,없는 RAM, 없는HDD을 만들어주는 도구)가 있어 매우 무겁고 느리다
* 반면 docker는 docker 엔진이라는 것이 os위에서 동작하는데 컨테이너라는 것을 생성할수 있다. 이렇게 하면 도커 이미지에 서버운영을 위한 프로그램과 라이브러리만 격리해 설치할수있고 os자원은 호스트와 공유할수 있다. 또한 하드웨서 추상화 계층(하이퍼바이저)가 없기때문에 메모리접근,네트워크속도등이 훨씬 가상머신보다 빠르다.
* LXC : 기존 리눅스에서 chroot라는 명령어로 루트디렉토리를 특정디렉토리로 바꿔주면 특정디렉토리는 chroot jail이라는 환경이 생성되고 해당 디렉토리는 외부의 디렉터리와 격리된 공간이 된다.chroot jail은 파일 시스템에 접근과 디렉토리, 네트워크 및 프로세스등을 컨트롤 할 수 있는데 LXC(LinuxX Countainer)가 jail과 유사한 개념으로 구현 되었다.리눅스 커널레벨에서 제공하는 일종의 격리된 가상공간이고 이곳엔 OS가 설치되지 않기에 **컨테이너**라고 부른다

* cgroup : 리눅스 커널의 cgroup은 OS가 관리하는 다양한 리소스를 중앙에서 제어하기 위한 도구이다. 리소스를 그룹화하고 각 그룹에 대해 우선순위 및 사용가능한 리소스를 제한하거나 그룹을 분리하는 등의 기능을 제공한다.cgroup에서 관리하는 대상은 파일 시스템이나 프로세스뿐만 아니라 CPU 리소스와 메모리, 각종 디바이스, 네트워크 패킷,네트워크 인터페이스 등 이다. 또한 CPU, 메모리, 디스크, 네트워크 자원등을 할당하여 완전한 형태의 가상공간을 제공한다. 또 프로세스트리,사용자 계정 파일시스템등을 격리시켜 호스트와 별개의 공간을만들고 이것을 **namespages** 라고 한다
=> LXC는 Cgroup과 namespace를 결합하여 애플리케이션을 위한 고립 환경을 제공한다. Docker또한 LXC를 실행드라이버의 하나로 사용할수 있으며 이를 통해 이미지 관리와 개발서비스를 제공한다. 현재는 독자적 컨테이너 드라이버를 개발해 사용하고 있다.

### 개발환경
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F49wTn%2FbtqASzgr8wR%2FjLuZ4oQHoMyv4CnkeTxBC0%2Fimg.png"/>

* 모놀리식과 마이크로서비스
 #### 모놀리식
 > 모놀리식 아키택처란 마이크로서비스 아키택처의 반대되는 개념으로 전통의 아키택쳐를 지칭하는 의미로 생겨난 단어이다.하나의 서비스 또는 애플리케이션이 하나의 거대한 아키택쳐를 가질떄 모놀리식 하다고 한다.
 
 * 모놀리식의 장점
  * 개발이 간단하다 -> 개발 툴이 단일 애플리케이션구축에 초점이 맞춰져 있다.
  * 쉽게 고가용성 서버 환경을 만들수 있다.
  * 배포하기 쉽다
  * 테스트가 쉽다
  * 확장하기 쉽다
  * 그러나 서비스 규모가 커질수록 장점이 빛바래간다.
 * 모놀리식의 단점
  * 어플이 커지면 커질수록 너무 복잡해셔 완전히 이해할수 없을 정도가 될 수도 있다. 또한 기능을 추가하면 추가할 수록 더 복잡하고 난해한 코드로 뒤덮힌다.(흔히 진흙공에 진흙을 덧붙힌다는 표현을 한다)
  * 어플리케이션의 크기때문에 IDE의 속도도 느려지고 빌드시간도 오래걸려 개발시간도 느려진다. 결국 생산성이 떨어지게 된다
  * 테스트시에도 오래걸리고 어플이 크다보니 철저한 테스트도 어렵다.
  * 전체가 같은 아키텍처다 보니 한부분에서 에러가 나면 서비스 자체가 죽어버리는 경우가 많다. 
 ##### 마이크로서비스란
 > 소프트웨어가 잘 정의된 API를 통해 통신하는 소규모 독립적인 서비스로 구성되어있는 경우의 소프트웨어 개발을 위한 아키택처 및 조직적 접근방식이다. 쉽게말해 하나의 어플을 여러 서비스로 기능 분해하는 아키택처이다.
 * 마이크로 아키택처는 서비스를 모듈성의 단위로 사용한다
 * 각 서비스는 다른 서비스가 함부로 넘어오지 못하게 각각의 API를 제공하며 우회하여 접근할 수 없게 한다
 * 서로 느슨하게 결합되어있고 오직 API를 통해서만 통신하는 것이다.
 * 이렇게 느슨하게 결합된 서비스들은 각각자체 DB를 가지게 된다.(DB자체가 아니라 독립된 스키마를 말한다.) 
 * 마이크로의 장점
  * 크고 복잡한 어플이라도 지속적 배포가 가능하다
  * 단일 서비스의 규모가 작아지기 때문에 관리가 쉽고, 개발자도 담당 서비스의 코드만 이해하면 되고 IDE역시 느려지지않아 생산성이 올라간다.
  * 서비스를 다른 담당 서비스 개발자와 협의 없이 독립적으로 배포/확장할수 있다.
  * 모든 팀은 독립적,자율적으로 움직일수 있고 개발 속도도 빨라진다.
  * 결함격리가 잘되기에 한 서비스에서 오류가 나도 다른서비스에 영향을 주지 않는다.
  * 새로운 기술을 서비스별로 실험하고 도입하기 쉽다.
 * 마이크로의 단점
  * 분산 시스템을 테스트 하기가 어렵다. 각 서비스의 테스트는 쉬워지지만 여러 서비스가 연관된 테스트를 진행하고 자동화하는 것은 쉬운일이 아니다.
  * 잘못 분해하면 결합도가 높은 서비스들로 구성된 분산 모놀리스를 구현하게 될 수도 있다.
  * 마이크로 서비스들 끼리 계속 서로 통신을 하다보니 모놀리식 아키택처에 비해 통신관련 오류가 잦다. 

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

* 컨테이너 기법
 * 컨테이너는 가상머신을 사용해 각 마이크로 서비스를 격리하는 기술이다
 * 컨테이너는 가상머신처럼 하드웨어를 전부 구현하지 않기 때문에 매우 빠른 실행이 가능하다 
 * 프로세스의 문제가 발생할 경우 컨테이너 전체를 조정해야 하기 때문에 컨테이너에 하나의 프로세스를 실행하도록 하는 것이 좋다
<img src="https://i1.wp.com/www.opennaru.com/wp-content/uploads/2014/11/Virtulization_vs_Container.png?fit=968%2C362"/>
 * 하이퍼바이저의 필요없는 공간을 더하면 더 많은 자원을 앱에 투자가능
 
## 2. 도커 레지스트리
> 도커 레지스트리에는 사용자가 사용할 수 있도록 데이터베이스를 통해 이미지를 제공해주고 있다. 누구나 이미지를 만들고 푸시할 수 있으며 푸시된 이미지는 다른 사람들에게 공유또한 가능하다.
<img src="https://user-images.githubusercontent.com/1712635/38648889-75bd77da-3da8-11e8-8f6d-7e45a30af502.png"/>
* 레지스트리를 조금더 간단하게 확인하는건 https://hub.docker.com/  도커허브라는 곳에서 더 자세하게 확인이 가능하다.
* 도커 자체에서 제공한 static 이미지도 있지만 사용자들이 업로드한 이미지들도 존재한다. 
 * static 이미지는 이미지명 만 존재 ex) mysql, node
 * 사용자 이미지는 사용자명/이미지명 이렇게 존재한다 ex) miny/movie-search-API
 
## 3. 도커의 라이프 사이클
<img src="https://postfiles.pstatic.net/MjAyMTA2MDZfMzIg/MDAxNjIyOTU1OTQxMjg2.mmoZUptx2hR0vCClFXCkXzD1GL_QxuetuxBCH-0cP_cg.K4GIorRdXwUCmyMvoeFKGYmrwdim1TKaQpJ6B_gqz0Qg.PNG.isc0304/image.png?type=w773"/>
 
1) 우선 도커 레지스트리에서 'pull' 명령으로 이미지를 다운받는다. 이때 image자체는 실행이 불가능한 파일이다
2) pull된 이미지를 실행하려면 컨테이너를 만들어 실행해야 되고 'create' 명령어를 통해 이미지를 컨테이너로 만들수 있다(=실행가능한 상태로 만들수 있다.)
3) 이제 컨테이너를 실행하기 위해 'start'명령어로 메모리에 올려 사용하게 된다
4) 그리고 이미지에 대해 'create'와 'start'를 한번에 해준는 명령어가 'run' 이다.  
5) 'pull'의 경우 풀링이 되어있는 이미지를 run할경우 위대로 'create','start'만 실행되지만 풀링이 되어있지 않은 이미지를 run하는경우 pull을 먼저 진행하게 된다 
6) run은 create와 start를 동시에 실행하는 명령인데 run을 실수로 2번 명령하면 똑같은 컨테이너가 2개 실행되는 꼴이다. 그렇기에 run은 create를 사용해야하는경우에만 사용하고 웬만하면 run과 start를 나눠서 사용하는편이 좋다고 한다.
* 실행 된 컨테이너를 중지시키는 명령은 stop
* 컨테이너를 삭제하는 명령어는 rm
* 이미지를 삭제하는 명령어는 rmi
* 사용한 컨테이너를 다시 이미지로 만들고 싶은경우 컨테이너를 commit 해서 이미지로 만들어 놓으면 나중에 다시 가져와 그대로 사용가능하다. ex) 개발환경에 필요한 DB, testDB, 웹서버등을 구축해놓고 이미지화해서 언제든지 사용가능 하게할 수 도 있을것 같다
 
## 4. 이미지 비밀: 레이어
> 이미지는 레이어별로 변경된 사항에 대해서 저장을 하는 방식이다.
 * 도커 이미지는 레이어라는 정보들로 구성이 된다. 
 * 도커 이미지 정보 확인
 > 도커 이미지의 매니페스트 파일을 확인하면 도커 이미지가 갖는 다양한 정보를 확인할 수 있다.
 ```
 sudo docker inspect nginx : nginx 이미지에 대한 정보를 확인 가능하다. 
 ```
 > /var/lib/docker/리눅스 내의 도커 저장소이다. 도커에서 이미지를 풀링 하면 docker디렉토리 아래에 도커에 관련된 여러 디렉토리가 있는데 이중 image와 overlay2라는 디렉토리가 있다. 이미지를 풀링하면 이미지가 image에 저장되고 이 이미지의 실질적 정보들은 overlay2에 저장된다고 한다.=> image 안엔 imagedb, layerdb가 있는데 imagedb는 layerdb의 정보를 가지고 있고 layerdb는 overlay2 디렉토리의 정보를 가지고 있다. overlay2는 이미지의 변경사항을  저장하는데 overlay2디렉토리 밑의 l 디렉토리에 저장이된다. 즉, 이미지의 간단한 데이터는 image에 그외 데이터들은 overlay2에 저장된다고 알아두자.


 * 도커 이미지 저장소 위치 확인
 ```
 sudo docker info
 sudo -i
 ```
 * 레이어 저장소 확인
 
 * 도커 용량 확인
  * du -sh /var/lib/docker/ #도커가 설치된 환경 용량 확인
   * 2.0G	/var/lib/docker/

  * du -sh /var/lib/docker/image/ # 도커 이미지에 대한 정보 저장 디렉토리
   * 2.7M	/var/lib/docker/image/

  * du -sh /var/lib/docker/overlay2/ # 도커 이미지의 파일 시스템이 사용되는 실제 디렉토리
   * 2.0G	/var/lib/docker/overlay2/

  * du -sh /var/lib/docker/containers/ # 도커 컨테이너 정보 저장 디렉토리
   * 136K	/var/lib/docker/containers/
cd 
 
 
## 도커 기본 명령어
> 도커는 명령어를 입력하는 클라이언트와 컨테이너를 관리하는 서버 이렇게 2개의 프로그램이 있다. 만약 10.1.1.1 서버에서 도커 서버를 설치하고 2000 포트로 오픈한다면 해당 서버에 직접 접속하지 않더라도 외부에서 2000포트로 도커 명령어를 전달 할 수 있다.
 ### run - 컨테이너 실행
 * docker run [option] image[:tag|@Digest] [commend] [arg....]
  * -d : 백그라운드 모드
  * -p : 호스트와 컨테이너의 포트를 연결
  * -v : 호스트와 컨테이너의 디럭토리를 연결
  * -e : 컨테이너 내에서 사용할 환경변수 설정
  * --name : 컨테이너 이름 설정
  * --rm : 프로세스 완료시 컨테이너 자동제거
  * -it : -i와 -t를 동시에 사용한 것으로 터미널 입력을 위한 옵션
  * --network : 네트워크 연결 
 * ubuntu 20.04 컨테이너 만들기
  ```
  docker run ubuntu:20.04 
  ```
  > run명령어를 사용하면 사용할 이미지가 저장되어있는지 확인하고 없다면 다운로드(pull)한 후 컨테이너를 생성하고 시작한다. 컨테이너는 정상적으로 실행됐지만 뭘 하라고 명령어를 전달하지 않았기 때문에 생성되자마자 종료된다. 컨테이너는 프로세스 이기 때문에 실행중이 프로세스가 없으면 컨테이너는 종료된다.조금더 자세하게 설명하면 도커 이미지마다 컨테이너가 만들어 질때 실행할 명령어를 지정할 수 있고 우분트20.04는 "/bin/bash" 가 지정되어 쉘이 실행되야 하지만 입력을 받을수 있도록 '-it' 옵션을 입력하지 않았기 떄문에 바로 실행이 종료되었다.
 * /bin/bash 실행
  ```
  docker run --rm -it ubuntu:20.04 /bin/sh
  ```
  > 컨테이너 내부에 들어가기 위해 sh를 실행하고 키보드 입력을 위해 -it옵션을 준다.  추가적으로 프로세스가 종료되면 컨테이너가 자동을 삭제 되도록 --rm 옵션도 추가한다. --rm 옵션이 없다면 컨테이너가 종료되더라도 삭제되지 않고 남아 있어 수동으로 삭제 해야 한다
 * 웹 어플리케이션 실행
 ```
 docker run --rm -p 5670:5678 hashicorp/http-echo -text="hello world"
 ```
 > -p 내 서버 포트 : hashicorp 포트번호로 연결하는 명령어 이다. 브라우저를 열고 localhost:5670을 접속하면 hello world 가 뜨게 되는 가상 웹서버를 토커 컨테이너로 띄운것이다.
 * Mysql 실행
 ```
 - mysql 이미지 실행
 docker run -d -p 3306:3306 \ -d로 백그라운드로 실행한다는 의미
   -e mysql_allow_empty_password=true \ mysql은 원래 비밀번호 입력해야 하지만 비밀번호 적용 안하겠다는 설정
   --name mysql \
   mysql:5.7
 - mysql 접속
 docker exec -it mysql mysql  : mysql 클라이언트로 접속하는 명령어
 exec : run 과 달리 실행중인 도커 컨테이너에 접속할 때 사용하며 컨테이너 안에 ssh server등을 설치하지 않고 exec명령어로 접속한다
 ```
 ### 도커 기본 명령어 2
 * ps 명령어
 ```
 docker ps : 실행중인 컨테이너 목록을 확인하는 명령어.
 docker ps -a : 중지된 컨테이너도 확인하려면 -a 옵션을 붙인다.
 ```
 * stop 명령어
 ```
 docker stop [option] container [container] : docker ps로 확인된 컨테이너 id나 names속성의 값을 option에 적용하면 컨테이너를 중지시킬수 있다.
 실행중인 컨테이너를 하나 또는 여러개 중지할 수 있다.
 ```
 * rm 명령어 
 ```
 docker rm [option] container [container] : stop으로 종료된 컨테이너를 완전히 제거하는 명령어 이다.
 ```
 * log 명령어
 ```
 docker logs [option] container : docker logs 컨테이너ID 시 프로그램이 실행되며 생긴 로그를 확인가능.
 -> 컨테이너가 정상적으로 동작하는지 확인하는 좋은 방법은 로그를 확인하는 것이다. 기본옵션과 -f, -tail 옵션.
 docker logs -f 컨테이너ID : 단순 logs는 로그를 보여주고 끝나는데 -f는 로그를 실시간 모니터링 해준다.
 ```
 * images 명령어
 ```
 docker images [option] [repository[:tag]] : docker images는 다운로드한 이미지 목록을 보는 명령어이다.
 ```
 * pull 명령어
 ```
 docker pull [option] name[:tag|@DIGEST] : 이미지를 다운로드하는 명령어이다. run시 이미지가 없으면 자동으로 다운로드(pull)하지만 수동 pull도 가능하다.
 docker pull ubuntu 18.04 -> 18,04버전의 우분투 이미지 다운로드 명령어이다.
 ```
 * rmi 명령어
 ```
 docker rmi [option] image [image...] : 다운 받은 이미지를 삭제하는 방법이다. images 명령어를 통해 얻는 이미지 목록에서 이미지 id를 입력하면 삭제가 된다. 단 컨테이너가 실행중인 이미지는 삭제되지 않는다.
 ex) docker rmi hasicorp/http-echo -> hasicorp/http-echo 이미지 삭제  
 ```
  * -f 를 붙이는 경우는 컨테이너가 떠있는데 이미지를 삭제하고 싶은경우에 주로 사용된다고 한다.
 
 * network create 명령어
 ```
 docker network create [option] network : 도커 컨테이너끼리 이름으로 통신할 수 있는 가상 네트워크를 만든다.
 ex) docker network create app-network -> app-network라는 이름으로 wordpress와 mysql이 통신할 네트워크를 만든다 
 ```
 * network connect 명령어
 ```
 docker network connect [option] network container : 기존에 생성된 컨테이너에 네트워크를 추가한다.
 ex) docker network connect app-network mysql : mysql 컨테이너에 app-network 네트워크를 추가한다.
 ```
 * network option 명령어
 ```
 docker run -d -p 8080:80 \
  --network=app-network \ 컨테이너를 돌릴때 워드프로세스의 네트워크를 app-network로 설정한다 
  -e wordpress_db_host=mysql \위에서 설정한 app-network에 mysql이 떠있고 워드프로세스의 디비는 mysql이라는 컨테이너 명을 설정해 둘이 같이 네트워크를 사용함으로서 설정이 가능하다.
  ...
 ```
 
 ### 호스트 및 컨테이너 간 파일 복사
 > 도커의 cp명령을 사용하면 파일을 복제해 컨테이너로 전달하거나 꺼낼수 있다. 호스트와 컨테이너,컨테이너 간의 파일을 복제할 수 있다.
 ```
 sudo docker cp <path> <to container>:<path> : 현 로컬에서 다른 컨테이너로 보내는 명령어
 sudo docker cp <from container>:<path> <path>  : 타 컨테이너에서 현 로컬로 가져오는 명령어
 sudo docker cp <from container>:<path> <to container>:<path> : 
 ```

 ### volume 명령어
 * volume mount(-v) 명령어
 > 만약 위의 mysql과 워드프로세스가 한 네트워크로 연결되어있는 상황에서 mysql 컨테이너를 stop시키고 rm 시켰을때 워드 프로세스가 죽게 되는데 이러한 경우를 방지하기 위해 데이터를 로컬에 저장해놓고 컨테이너 구동시에 voulme mount(-v)명령어를 넣어주어 데이터를 끌어다 사용할 수 있다.
 ```
 ex) -v /my/own/datadir:/var/lib/mysql  -> 로컬의 디렉터리 주소: 컨테이너의 내부 주소를 연결하는 명령어. 즉,로컬의 디렉터리인 /my/own/datadir 에 컨테이너 내부 주소인 /var/lib/mysql의 데이터를 저장한다는 명령어이다.
 ```
 ### 도커 이미지 만들기
 > 도커의 이미지란?
 * 이미지는 프로세스가 실행되는 파일의 집합(환경)이다. 프로세스는 환경(파일)을 변경할 수 있다
 * 이 환경을 저장해 새로운 이미지를 만들수 있다
 * 기존 우분투 이미지에 git이 설치된 새로운 이미지 파일인 ubuntu/git01이라는 이미지를 만들 수 있다
  * 여기서 이미지 name이 ubuntu인것과 ubuntu/git01의 차이는 단순 ubuntu는 기본 이미지 이고 ubuntu/git01은 커스텀 이미지 즉 인위적으로 무언가 추가된 이미지 이다.
 ```
 이미지 생성 명령어
 docker build -t miny/ubuntu:git01 .
 |명령어|  -t   |이름공간| / |이미지이름|:|태그| .
 ```
  #### Dockerfile
  * 도커 이미지를 컨테이너로 run할시 dockerfile이 해당 디렉토리에 있다면 dockerfile의 내용을 이미지에 적용해준다.
  ```
  우분투 이미지를 run하는 디렉토리의 dockerfile에
  FROM ubuntu:latest
  RUN apt-get update
  RUN apt-get install -y git
  와 같은 명령어를 명시하면 우분투 이미지가 컨테이너로 실행되면 git의 최신 버전을 설치하는 작업을 자동으로 진행해준다.
  ``` 
   
