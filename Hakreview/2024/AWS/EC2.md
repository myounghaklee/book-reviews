AWS billing information은 IAM 사용자 접근을 허용하면 IAM 사용자도 접근할 수 있다.

# EC2
Elastic Compute Cloud의 약자이며 서비스형 인프라스트럭쳐이다.
- 가상머신 rental(EC2)
- 데이터를 가상 드라이브, EBZS볼륨에 저장(EBS)
- Elactric Load Balancer를 사용해 로드를 분산시킬 수 있다.(ELB)
- auto scaling을 통해 서비스를 확장 할 수 있다.(ASG)

## EC2 Sizing, configuration options
- OS 선택 : linux, windows, mac os
- cpu(core 수) 선택
- ram 선택
- storage space ( ebs, efs, ec2 instance store) 선택
- network card 선택 : ec2인스턴스에 연결할 네트워크 종류 선택, 속도가 빠른 카드를 사용할지 공용 IP를 사용할지   
- 방화벽 규칙 선택
- 인스턴스를 구성하기위한 bootstrap 스크립트(최초에는 user data만 있다)

### EC2 User data
- bootstrapping은 머신이 시작할때 최초에만 실행된다.부팅작업을 자동화 하기때문에 bootstrapping이라는 이름을 가지게된다.
- ec2 user data는 특정한 목적이 있다.
    - update, software 설치
    - 인터넷의 공용파일 다운로드

사용자 데이터 스크립트는 루트 계정에서 실행되기 때문에 `sudo` 로 실행해야된다.


## EC2 Types

### Instance naming convention
m5.2xlarge
- m : instance class
- 5 : 세대
- 2xlarge : 인스턴스 클래스의 크기

### 목적
- 범용 인스턴스 : 웹서버, 코드 저장소와 같은 다양한 작업에 적합하다.
- 컴퓨팅 최적화 인스턴스 : 컴퓨터 집약적인 작업에 최적화되어있으며 아래와 같은 목적으로 사용한다. 또한 C로 시작하는 명명규칙을 따른다.
    - batch processing
    - media transcoding
    - high performance web servers
    - high performance computing (HPC)
    - machine learning
    - 전용 gaming server
- 메모리 최적화 인스턴스 : 대규모 데이터셋을 처리하는 유형의 작업에 적합하다. 아래의 경우에 사용한다. 기본적으로 네임이 규칙은 R로 시작하지만 x1, z1와 같은 대용량 메모리도 있다.
    - 고성능 db
    - 분산 웹스케일 캐쉬 저장소
    - Business intelligence에 최적화된 인메모리 데이터베이스

- 스토리지 최적화 인스턴스
로컬 스토리지에서 대규모 데이터셋에 접근할떄 적합한 인스턴스.아래의 경우에 사용한다. 기본적으로 네이밍 규칙은 I,D,H1으로 시작한다.
    - OLTP(High frequency online transaction processing) 시스템
    - database
    - 인메모리 db를 위한 cache(Redis)
    - data warehousing application
    - 분산파일 시스템
## Security Groups
- 트래픽 제어
- 허용 규칙만 포함
- ip주소를 참조해 규칙을 만들 수 있다. 보안그룹끼리도 참조할 수 있다.
- 통제 범위
    - 포트 접근
    - authorised ip ranges (IPv4, IPv6)
    - i/obound 네트워크

### Security Group이란
- 여러 인스턴스에 적용할 수 있다.
- region/VPC 조합으로 통제한다. 그래서 지역을 전환하면 새 보안그룹을 생성하거나 다른 VPC를 생성해야한다. 
- EC2 밖에 존재한다. 
- SSH 액세스를 위해 하나의 별도 보안 그룹을 유지하는것이 좋다.
- timeout으로 application에 접근할 수 없다면 보안그룹 문제다.
- 연결거부 오류가 발생하면 이건 application의 문제다.
- 기본적으로 모든 인바운드 트래픽은 거절, 아웃바운드는 허용된다.

### Classic ports to know
- 22 : SSH linux에서 ec2 인스턴스로 로그인 가능하도록 함
- 21 : FTP 파일전송 프로토콜, 파일을 업로드하는데 사용됨
- 22 : SFTP(Secure File Transfer Protocol) ssh를 이용한 파일업로드
- 80 : http 접근용
- 433 : https 접근용
- 3389 : RDP(Remote DeskTop Protocol) 윈도우 인스턴스에 로그인할때 사용

## SSH
CLI를 통해서 원격머신이나 서버를 제어할 수 있게 해준다.
### SSH 실행방법
```
### key file directory 이동
### public ip 확인후 아래 command 입력
ssh -i EC2Tutorial.pem ec2-user@3.87.167.84
### 만약 권한이 없을경우
chmod 0400 EC2Tutorial.pem

### 입력후 ec2-user로 접속 되었다면 
whoami
```
### SSH Trouble shooting

1. 연결 시간 초과가 발생하는 경우

보안 그룹에 문제가 있는 경우 발생합니다. 모든 유형의 시간 초과(SSH뿐만 아니라)는 보안 그룹 또는 방화벽과 관련 있습니다. 보안 그룹이 다음과 같이 설정되었는지 확인하고, EC2 인스턴스에 올바르게 할당되었는지 확인

2. 연결 시간 초과 문제가 계속해서 발생할 경우

위와 같이 보안 그룹이 올바르게 설정되었으나 여전히 연결 시간 초과 문제가 발생하는 경우, 회사 방화벽 또는 개인 방화벽이 연결을 차단한다는 뜻입니다. 다음 강의에서 설명하는 대로 EC2 Instance Connect를 사용하세요.

3. SSH가 Windows에서 동작하지 않을 경우
- ssh command not found라고 뜨면 PuTTY를 사용해야 합니다.
- 동작하지 않는다면 다음 강의에서 설명하는 대로 EC2 Instance Connect를 사용하세요.

4. 연결이 거부될 경우

이는 인스턴스에는 도달할 수 있지만 해당 인스턴스에서 SSH 유틸리티가 실행되고 있지 않다는 뜻. 인스턴스를 재시작하세요.

여전히 동작하지 않는다면, 인스턴스를 종료하고 새 인스턴스를 만드세요. 반드시 Amazon Linux 2를 사용하세요.

5. 권한 거부(publickey,gssapi-keyex,gssapi-with-mic)

이는 다음의 두 상황에서 발생합니다.

- 보안 키가 틀렸거나 보안 키를 사용하지 않는 경우입니다. EC2 인스턴스 구성을 확인하여 올바른 키를 할당했는지 확인하세요.

- 잘못된 사용자를 사용할 경우에도 발생합니다. Amazon Linux 2 EC2 인스턴스에서 실행했는지 확인하고, ec2-user 사용자를 사용하고 있는지 확인하세요. SSH 명령어 또는 PuTTY 구성에서 ec2-user@<public-ip> (ex:ec2-user@35.180.242.162) 코드를 실행할 때 지정해야하는 사항입니다.


6. 어제는 연결이 가능했는데 오늘은 되지 않습니다.

EC2 인스턴스를 중단했다가 오늘 다시 시작했을 경우, 이런 일이 발생할 수 있습니다. 이 경우, EC2 인스턴스의 퍼블릭 IP가 변경됩니다. 따라서 명령어 또는 PuTTY 구성에서 새 퍼블릭 IP로 업데이트하여 저장.


## EC2 구매 Options

- 예약 인스턴스 : 기간은 1년 또는 3년, 오랫동안 db를 운영할 계획일때 사용
    - 유연한 인스턴스 타입을 원할때 : 전환형 예약 인스턴스

- 절약 계획 : 1년,또는 3년이며 달러 단위로 특정한 사용량을 약정하는것, 장기적으로 상요할때 사용
- spot instance : 짧은 워크로드를 위해 사용, 하지만 인스턴스가 손실될 수 있어 신뢰성이 낮음
- 전용 호스트 : 물리적 서버 전체를 예약해서 인스턴스 배치를 제어할 수 있음. 
- 전용 인스턴스 : 다른 고객이 내 하드웨어를 공유하지 않는다는 의미
- 용량 예약 : 원하는 기간동안 특정한 AZ에 용량을 예약할 수 있다.


### EC2 On Demend
- 사용한만큼 청구된다.
    - Linux, Windows : 1분 이후에 초 단위로 청구
    - 다른 운영체제 : 1시간마다 청구
- 비용이 가장 많이 들지만 바로 지불할 금액은 없으며 장기적인 약정도 필요 없다.
- 단기적이고 중단 없는 워크로드가 필요할 때, 애플리케이션을 예측할 수 없을 때 사용

### EC2 예약 인스턴스
- 온디맨드에 비해 72%할인 제공
- 인스턴스 타입, 리전, 테넨시, OS를 예약
- 예약 기간을 1년, 3년으로 지정해서 할인들 더 받을 수 있고 모두 선결제이다.
- 전환형 예약 인스턴스
    - 인스턴스 타입, 패밀리, 운영체계, 범위 테넨시를 변경할 수 있다.
    - 최대 66% 할인 받을 수 있다

### 절약 계획 
- 장기적으로 사용하면 할인받을 수 있다.(최대 72%)
- 다음 1년내지 3년동안 시간당 10달러로 약정을 하게 된다. 
- 사용량이 한도를 넘어가면 절약플랜은 온디맨드 가격으로 청구하게된다.
- 절약 플랜을 사용하면 특정한 인스턴스와 패밀리, 리적으로 고정되게 된다.

### Spot Instance
- 할인폭이 제일커서 온디맨드에 비해 최대 90%까지 할인 가능
- 스폿인스턴스에 대해 지불하려는 최대 가격을 정의하고 만일 스폿 가격이 그 가격을 넘게되면 인스턴스가 손실된다.
- 스폿 인스턴스는 AWS에서 가장 비용효율적인 인스턴스이며 인스턴스가 고장에 대한 회복력이 있다면 유용하다.
- 배치, 데이터분석, 이미지처리, 모든 종류위 분산형 워크로드, 시작과 종료가 유연하 ㄴ워크로드에 유용하다 

### 전용 호스트 
- 전용으로 사용되는 EC2 인스턴스 용량이 있는 실제 물리서버를 받게 되며 법규 준수요건이 있는 활용 사례나, 소켓, 코어, VM소프트웨어 라이센스 기준으로 청구되는 기존의 서버에 연결된 소프트웨어 라이센스가 있는경우
- 온디맨드로 초당 비율을 지불하거나 1년, 3년으로 예약할 수 있다.

<b> 전용 인스턴스란 자신만의 인스턴스를 자신만의 하드웨어에 갖는다는 반면 전용 호스트는 물리적 서버 자체에 대한 졉근권을 가지고 낮은 소존의 하드웨어에 대한 가시성을 제공해준다는 점</b>


# EC2 Instance Storage
## EBS
- EBS Volume : Elastic Bloc Store의 줄임말이며 인스턴스가 실행중이 ㄴ동안 연결 가능한 네트워크 드라이브다.
    - EBS Volume을 사용하면 인스턴스가 종료된 후에도 데이터를 지속할 수 있다. EBS 볼륨을 마운트 하면 데이터를 다시 받을 수 있다.
    - CCP레벨(하나의 EBS는 하나의 EC2인스턴스에만 마운트 가능)의 EBS 볼륨은 하나의 인스턴스에 마운트
    - 특정한 존에서만 사용가능하다. a존에서 b존으로 마운트 불가능
    - <b>네트워크 UBS 스틱이라고 생각하면 된다.</b>
    - Free tier는 매달 30GB의 EBS 스토리지를 범용 SSD, 마그네틱 유형으로 제공된다.

### EBS 종료시 삭제
콘솔에서 EBS볼륨을 생성하면 `종료시 삭제` 옵션이있다. 기본 설정으로 root 볼륨에서는 체크가 되어있고 새로 생성하는 볼륨은 체크가 안되어있다.
인스턴스가 종료될때 루트 볼륨을 유지하고자 하는 경우 루트볼륨의 종료시 삭제 옵션 체크를 빼면된다.

### EBS Snpashots
- 어느 시점에 돌아갈 백업 포인트를 만드는것, 필수는 아니지만 권장사항
- 다른 가용영역이나 region에도 복사할 수 있다. 

### 특징
EBS Snapshot Archive
- 스냅샷을 archive tier에 옮기며 75프로 정도 저렴하다
- 24072시간 이내에 복원가능하다(즉시복원 x)

EBS Snapshot recycle bin
- 스냅샷을 삭제하는 경우 영구 삭제하는 대신에 휴지통에 넣을 수 있다.
- 복원할 수 있으며 보관기간은 1일에서 1년이다.

Fast Snapshot Restore

### AMI
Amazon Machine Imag로 EC2인스턴스를 통해 만든 이미지다.
- ami에 소프트웨어, 설정, OS, 모니터링툴들을 추가할 수 있다. 
- 따로 구성하며 부팅 및 설정에 드는 시간을 줄일 수 있다.
- 특정한 리전에 구축후에 복사해서 글로벌 인프라로 활용할 수 있다. 
- public AMI :  aws에서 제공하는 것
- own AMI : 직접만들어서 유지, 관리도 직접한다
- AWS MarketplaceAMI : 기업이 자체적으로 AMI를 구성해 자신들이 만든 소프트웨어를 넣고 구성까지 마친다음에 마켓플레이스를 통해 판매하는것을 사용


### AMI Process
- EC2를 띄운 후에 데이터 무결성을 위해서 종료한다.
- 이 인스턴스를 바탕으로 AMI를 구성하고 EBS 스냅샷을 찍는다.

### EBS Volume
EBS 볼륨은 6가지 타입이 있다
- gp2/gp3(SSD) : 범용 SSD볼륨으로 다양한 워크로드에 대해 가격과 성능의 균형을 맞춘다
- io1/io2(SSD) : 최고성능의 SSD볼륨으로 미션 크리티컬, 지연시간이 낮고 대용량 워크로드에 사용
- st1(HDD) :  낮은 비용의 HDD볼륨으로 잦은 접근과 처리량이 많은 워크로드에 사용
- sc1(HDD) : 가장 비용이적게들며 낮은 접근을 시도하는 워크로드에 사용

EBS볼륨은 크기, 처리량, IOPS(초당 I/O)
`시험에 범용 gp2와 IOPS 프로비저닝이 주로 출제`

#### gp2
- 짧은 지연시간을 자랑하며 효율적인 비용의 스토리지
- 시스템 볼륨에서 가상 데스크톱, 개발, 테스트 환경에서 사용할 수 있다
- 크기는 1GB에서 16TB까지 가능
- gp3 :
    - 가장 최신으로 3000IOPS와 125MB/s 처리량을 제공
    - IOPS 처리량은 최대 16000, 처리량은 1000MB/s까지 증가시킬 수 있다
- gp2 : 
    - 작은 볼륨으로 최대 IOPS 3000까지 증가시킬 수 있다
    - 볼륨과 IOPS가 연결되어있어있을떄 최대 IOPS 16000이다
    - 5334GB일때 3IOPS per GB는 16000을 초과한다

#### provisioning된 IOPS
- IOPS성능을 유지할 필요가 있는 application 이나 16000 IOPS이상을 요하는 application에 적합하다.
- DB워크로드에도 알맞으며 스토리지를 이용하는 경우에도 적합하다. 스토리지 성능과 일관성에 아주 민감한데 이럴때 io1.io2볼륨으로 바꾸는것이 좋다
    - Max PIOPS : Nitro EC2인스턴스에서는 최대 64000까지 가능하며 아닌경우에는 32000 IOPS까지 가능
    - 또한 이를 이용하면 gp3볼륨처럼 프로비저닝된 IOPS를 스토리지크기와 독자적으로 증가시킬 수 있다
    - io2는 io1과 동일한 비용으로 내구성과 기가당 IOPS의 수가 더 높다, 
- io2 블록 익스프레스(4GB - 64TB):
    - 고성능 유형의 볼륨
    - 지연시간이 밀리초 미만이며 IOPS:GB  비율이 1000:1일 때 최대 256000 IOPS를 자랑한다

### EBS 다중연결(io1/io2에서만 가능)
- 다중연결 기능이 활성화된 io2볼륨이 있을때 이볼륨을 여러개 EC2인스턴스에 동시 연결할 수 있다. 
- 각 인스턴스는 고성능 볼륨에 대한 읽기, 쓰기 권한을 전부 가져서 동시에 읽고 쓸 수 있다.
- 해당 가용영역에서만 사용할 수 있다. 한 AZ에서 다른 AZ로 EBS볼륨은 연결할 수 없다.
- 한번에 `16` 개의 EC2인스턴스만 같은 볼륨에 연결할 수 있다.
### EBS 암호화
EBS볼륨을 생성하면 즉시 저장데이터가 볼륨 내부에 암호화되고 인스턴스 보륨간의 전송 데이터 역시 암호화된다.
- 스냅샨, 스냅샷으로 생성한 볼륨역시 모두 암호화된다.
- 이떄 암호화 및 복호화 매커니즘은 보이지 않게 처리되어 아무것도 하지 않아도 되며 EC2dhk EBS가 백그라운드에서 모두 처리한다.
- 암호화를 사용해야 하는 이유느 ㄴ지연시간에 영향이 거의 없고 KMS에서 암호화키를 생성해 AES-256암호화 표준을 갖는다.


### EFS - Elastic File System
 - 관리형 NFS로 네트워크 파일 시스템이다. 많은 EC2 인스턴스에 마운트 될 수 있따.
 - EC2는 서로 다른 가용영역에 있을 수 있어서 모든 EC2가 EFS에 연결할 수 있따. 
 - 가용성이 높고 확장성이 뛰어나며 비싸다. GP2 EBS볼륨의 약 3배정도. 사용량에 따라 비용을 지불하여 미리 용량을 프로비저닝할 필요가 없다.
- 사용 사례 : 콘텐츠 관리, web serving, 데이터 공유, 워드프레스
- 내부적으로 NFS프로토콜을 사용하며 EFS에 대한 액세스를 제어하려면 보안그룹을 설정해야한다.
- 윈도우가 아닌 Linux기반 AMI와만 호환가능하다
- KMS를 사용해서 EFS 드라이브에서 미사용 암호화를 활성화 할 수 있다. 따라서 Posix시스템을 사용하며 표준파일 api가 있다. 
- 파일시스템은 자동ㅇ으로 확장되며 EFS에서 사용하는 데이터 GB사용량에 따라 비용을 지분한다.

### EFS - 성능, 스토리지 클라스
- EFS스케일
    - 동시 NFS 클라이언트 수천개와  10GB이상의 처리량을 확보할 수 있다.
    - PetaByte 규모의 네트워크 파일 시스템으로 자동 확장할 수 있다.
- 네트워크 파일 시스템 생성시 성능 모드를 설정할 수 있으며 여러가지옵션이 있다.
    - 범용모드 : 지연시간에 민감한 사용사례에 사용된다(웹서버, CMS)
    - Max I/O : 처리량을 최대하하며 지연시간이 더 길지만 초리량이 높고 병렬성이 높다.(빅데이터 애플리케이션, 미디어처리 필요한경우 유용)
- 처리량 모드
    - 버스팅 : 초당 50Mib/s + burst of up to 1000MiB/s
    - provisioned : 스토리지 크기에 관계없이 처리량을 설정하구 싶은경우에 사용하며 스토리지가 늘어날 수록 처리량이 증가했었지만 프로비저닝을 사용하면 1TB의 스토리지에서 초당 1GiB를 처리할 수 있다. 스토리지와 처리량을 분리했기때문에 괜찮다.
    - Elastic : 워크로드에 따라 처리량을 자동으로 처리할 수 있다.