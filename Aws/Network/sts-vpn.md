# Site to Site VPN

### AWS Site-to-Site VPN란 무엇인가요?

기본적으로 VPC내 리소스 중 대부분은 자체로 네트워크와 통신 할 수 없습니다.AWS Site to Site VPN 연결을 통해 VPC 에서 원격 네트워크에 대한 액세스를 활성화 할 수 있습니다.

### Site to Site 구성 요소

1. 가상 프라이빗 게이트웨이(Virtual Private Gateway)
2. 전송 게이트웨이 (Transit Gateway)
3. 고객 게이트웨이 (Customer Gateway)

가상 프라이빗 게이트웨이(Virtual private gateway): 단일 VPC에 연결할 수 있는 사이트 간 VPN 연결의 Amazon 측 VPN 엔드포인트입니다.
전송 게이트웨이(Transit gateway): 사이트 간 VPN 연결의 Amazon 측 VPN 엔드포인트로 여러 VPC와 온프레미스 네트워크를 상호 연결하는 데 사용될 수 있는 전송 허브입니다.

### Site to Site 실습 해보자.

먼저 제가 구성할 Site to Site VPN은 Static 타입으로 온프레미스 역할을 하는 인스턴스가 존재하는 VPC와 다른 VPC를 연결한다. 이 두 VPC 는 서로 연결되어있지 않으며, 오로지 VPN 연결만 있을 예정이다.

