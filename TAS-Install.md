# 설치 방법

## TAS 설치

#### VMware Tanzu Application Service

#### VM용 TAS 리소스 요구사항

- 일반적인 요구사항





#### VM용 TAS 구성

**전제 조건**

이 절차를 시작하기 전에 다음을 수행해야 합니다.

- Ops Manager에 맞게 환경을 준비하고 BOSH Director를 설치 및 구성하는 단계를 성공적으로 완료했는지 확인하십시오.



**Ops Manager에 VM용 TAS 추가**

VM용 TAS를 구성하려면 먼저 Ops Manager 설치 대시보드에 VM용 TAS 타일을 추가해야합니다.

IMPORT A PRODUCT 버튼을 클릭하여 Ops Manager에 추가합니다.



- **Assign AZs and Networks**

![](assingaz01.png)

1.  ****Place singleton jobs****: 첫 번째 AZ를 선택합니다. Ops Manager는 이 AZ에서 단일 인스턴스로 모든 작업을 실행합니다.

2. **Balance other jobs**: Ops Manager는 지정한 AZ에서 작업 인스턴스 둘 이상의 인스턴스와 균형을 맞춥니다.(고가용성을 위하여 최소 3개의 AZ를 권장합니다.)

3. **Network**: BOSH Director 타일을 구성할 때 생성한 런타임 네트워크를 선택합니다.

##### 모든 설정 입력 후 Save를 클릭합니다.



- **Domains**

![](domains01.png)

1.  **System domain**: System 도메인으로 사용할 주소를 입력합니다. ex. sys.ds.lab

2. **Apps domain**: Apps 도메인으로 사용할 주소를 입력합니다. ex. apps.ds.lab



##### 모든 설정 입력 후 Save를 클릭합니다.



- **Networking**

![](networking09.png)

1. **Gorouter IPs 및 HAProxy IPs** : HAProxy를 사용하는 여부에 따라 설정을 진행합니다. 

※ *Note:  Gorouter IP 또는 HAProxy IP 필드에서 특정 IP 주소를 할당하도록 선택한 경우 이러한 IP 주소가 Ops Manager에서 VM용 TAS에 대해 구성한 서브넷에  있는지 확인하십시오.*

| HAProxy 사용여부 | Gorouter IP 필드                                                                                                                                                                                   | HAProxy IP 필드                                                                                                                                                    |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| NO           | Ops Manager에서 구성한 서브넷에서 IP 주소를 선택합니다.
Gorouter IP 필드 에 이러한 IP 주소를 입력합니다 . 고가용성을 위해 둘 이상의 IP 주소를 지정해야 합니다. IP 주소는 서브넷 CIDR 블록 내에 있어야 합니다. 배치를 위해 구성한 도메인에 대한 요청을 이러한 IP 주소로 전달하도록 로드 밸런서를 구성하십시오. | 공백                                                                                                                                                               |
| YES          | 공백                                                                                                                                                                                               | Ops Manager에서 구성한 서브넷에서 IP 주소를 선택합니다.
HAProxy IP 필드 에 이러한 IP 주소를 입력합니다 . 고가용성을 위해 둘 이상의 IP 주소를 지정해야 합니다.
배치를 위해 구성한 도메인에 대한 요청을 이러한 IP 주소로 전달하도록 로드 밸런서를 구성하십시오. |

2. (선택) **SSH Proxy IPs 및 TCP router IPs** : SSH Proxy 포트 2222의 앱 컨테이너에 대한 SSH 요청을 수락하는 Diego Brain IP 주소를 추가합니다. TCP router에 할당하려는 IP 주소를 추가합니다. 이 창 하단에서 이 기능을 활성화 합니다. 

3. **Certificates and private keys for the Gorouter and HAProxy** : 하나 이상의 인증서와 개인 키 및 인증서 키 쌍을 Gorouter 및 HAProxy에 제공해야합니다. Gorouter 및 HAProxy 는 기본적으로 TLS 통신을 수신하도록 활성화 되어 있습니다. 


