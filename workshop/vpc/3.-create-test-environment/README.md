---
description: 생성하였던 VPC 끼리 통신이 가능한지 확인합니다.
---

# 3. Create Test Environment

각 VPC 내 서브넷에, 테스트 환경을 구성 해 보겠습니다. 이번 단계의 구성 목표는 아래 이미지와 같습니다.

먼저 처음 생성하였던 VPC(VPC-Lab)의 경우 Internet Gateway를 부착하여 인터넷을 통해 접근 할 수 있도록 구성되어있습니다. 이에따라 해당 VPC 에 Ec2 를 생성할 경우 인터넷을 통해 접근이 가능합니다. 이를 확인 해보겠습니다.

## Ec2 키페어 생성

인스턴스에 접속하여 테스트 진행 시 사용하게 될 키페어를 미리 생성 해보도록 하겠습니다.

[EC2 콘솔에](https://ap-northeast-2.console.aws.amazon.com/ec2/home?region=ap-northeast-2) 접근한 후, 좌측 카테고리에서 "키페어"를 선택합니다 .

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption><p>Ec2 키페어 생성</p></figcaption></figure>

키페어 생성 페이지로 접근 한후 이름과 유형 그리고 형식을 선택합니다.

유형의 경우 RSA 로 선택 해주시며, 키페어 형식은 Putty 를 사용할 경우엔 ppk 를 선택 하고, 일반적으로 shell 을 통해 ssh 를 접근 할 경우 pem 형식을 선택합니다.

모두 선택 완료 하였다면, 우측 아래 키페어 생성을 클릭하면 브라우저에서 키페어를 생성합니다.

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

## EC2 인스턴스 생성



[EC2 콘솔에](https://ap-northeast-2.console.aws.amazon.com/ec2/home?region=ap-northeast-2) 접근 후,   좌측 카테고리에서 "인스턴스"를 클릭한 후, 우측 상단의 인스턴스 시작 버튼을 클릭합니다.

<div align="center">

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>VPC 생성 콘솔 진입</p></figcaption></figure>

</div>

### Ec2 컴퓨팅 정보 입력

아래 표를 참고하여 인스턴스를 컴퓨팅 환경을 구성하는 기본 적인 정보를 입력 하도록 하겠습니다.&#x20;

아래 표 이외의 옵션들은 수정 하지 않은 상태로 진행 하겠습니다.

<table><thead><tr><th width="146">이름</th><th width="589">값</th></tr></thead><tbody><tr><td>이름 </td><td>ec2-lab</td></tr><tr><td>AMI </td><td>Amazon Linux 2023 ami</td></tr><tr><td>아키텍쳐</td><td>64비트(x86)</td></tr><tr><td>인스턴스 유형</td><td>t2.micro</td></tr><tr><td>키페어</td><td>이전 단계에서 생성한 키페어 선택</td></tr></tbody></table>

\
아래 이미지를 참고하여 위 표의 정보를 입력합니다.

<div data-full-width="true">

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>이름 및 AMI 지</p></figcaption></figure>

</div>





<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption><p>인스턴스 유형 및 키페어 지정</p></figcaption></figure>



### Ec2 네트워크 및 보안그룹 구성 정보 입력

컴퓨팅 구성 정보를 입력하였다면, 아래로 스크롤 하여 네트워크 설정탭에서 인스턴스의 네트워크 관련 구성정보를 기입합다.

아래 표를 참고하여 네트워크 관련 정보를 입력 하도록 하겠습니다.

<table><thead><tr><th width="182">이름</th><th>값</th></tr></thead><tbody><tr><td>VPC </td><td>VPC-Lab</td></tr><tr><td>서브넷</td><td>Public Subnet A</td></tr><tr><td>퍼블릭 IP 자동 할당</td><td>활성</td></tr><tr><td>보안그룹</td><td>보안그룹 생성을 선택하고, SSH 포트에 대해 자신의 IP 를 허용하는 규칙 생성</td></tr><tr><td>보안그룹 이름</td><td>VPC-Lab EC2 Secuirty Group ( 자유롭게 지정하셔도 됩니다 )</td></tr></tbody></table>

위 표에 적힌 정보를 모두 선택 및 기입 하였다면 우측 인스턴스 시작 버튼을 눌러 인스턴스를 시작합니다.

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption><p>네트워크 정보 입력 및 인스턴스 생성</p></figcaption></figure>

인스턴스가 정상적으로 생성되기까지 1\~2분 정도 소요 될 수 있습니다.



### EC2 인스턴스 생성 2

&#x20;생성한 2개의 VPC 끼리 통신이 가능한지 확인하기 위해, 생성하였던 VPC-Peer 내부에도 인스턴스를 생성해보도록 하겠습니다.&#x20;

기존 방법과 동일하지만, 아래 구성만 아래 표를 참고하여 생성 해주시면 감사하겠습니다.

<table><thead><tr><th width="182">이름</th><th>값</th></tr></thead><tbody><tr><td>이름</td><td>peering-test</td></tr><tr><td>VPC </td><td>VPC-Peer</td></tr><tr><td>서브넷</td><td>Peer Private Subnet A</td></tr><tr><td>퍼블릭 IP 자동 할당</td><td>비 활성화</td></tr><tr><td>보안그룹</td><td>보안그룹 생성을 선택하고, 모든 ICMP IPv4 프로토콜에 대해서 위치무관하게 허용하도록 규칙을 구성합니다.</td></tr><tr><td>보안그룹 이름</td><td>VPC-Peer EC2 Secuirty Group ( 자유롭게 지정하셔도 됩니다 )</td></tr></tbody></table>

해당 인스턴스에는 접속 하지 않을 예정이기 때문에, 키페어는 구성 하지 않아도 됩니다.



### 인스턴스 접속 테스트

인스턴스가 생성 완료 되었다면, 해당 인스턴스로 SSH 접속을 시도 해보겠습니다.

선택한 키페어의 확장자에  따라 ( pem , ppk ) 접속 방법이 달라지기 때문에, 다음 항목의 문서를 참고 하여 테스트 진행 해주시면 될 것 같습니다.
