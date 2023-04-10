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
   
   - Name : 생성하고자 하는 이름
   
   - Certificate and private key 아래 Generate RSA Certificate 클릭 
     
     - ex) `*.DOMAIN.com`, `*.apps.DOMAIN.com`, `*.sys.DOMAIN.com`,`*.login.sys.DOMAIN.com`, `*.uaa.sys.DOMAIN.com` 
   
   - 추가 필요 시 Add 버튼을 클릭

4. (선택)**Certificate Authorities trusted by the Gorouter** : 상호 TLS를 사용하여 클라이언트 요청을 검증할 때 Gorouter는 기본적으로 CA를 신뢰합니다. Gorouter와 동일한 CA를 신뢰하도록 HAProxy를 구성하려면 해당 칸에 CA인증서를 입력합니다. 

5. (선택)**Enables the HTTP/2 protocol for communicating with apps** : Gorouter에 대한 HTTP/2 수신 및 송신을 비활성화하려면 해당 칸을 선택 취소합니다. HTTP/2는 기본적으로 활성화 되어있습니다. 

6. **Select the range of TLS versions supported by the Gorouter and HAProxy** : Gorouter 및 HAProxy 통신에서 사용할 TLS 버전 범위를 선택합니다. 

7. (선택)**Balancing algorithm used by the Gorouter** : Gorouter에 대한 로드 밸런싱 알고리즘을 선택합니다. 기본 옵션인 라운드로빈을 권장합니다. 

8. **Logging of client IPs in the Gorouter** : Log client IPs 옵션은 기본적으로 설정됩니다. 로드 밸런서가 자체 소스 IP 주소를 노출하는 경우 Disable logging of X-Forwarded-For header only를 로드 밸런서가 원래 클라이언트 소스 IP를 노출하는 경우 Disable logging of both source IP and X-Forwarded-For header를 선택합니다.

9. **TLS termination point** : x-forwarded-client-cert 배포에서 TLS가 처음 종료되는 위치를 기반으로 VM용 TAS HTTP 헤더를 처리하는 방법으로 구성합니다.
   
   | Deployment Configuration                                                                                   | TLS Option                   | Additional Notes                                                                |
   | ---------------------------------------------------------------------------------------------------------- | ---------------------------- | ------------------------------------------------------------------------------- |
   | - 로드 밸런서가 TLS를 종료<br>- 로드 밸런서는 상호 인증 TLS handshake의 클라이언트 인증서를 x-forwarded-client-cert HTTP 헤더에 넣도록 구성됩니다. | Infrastructure load balancer | HAProxy와 Gorouter 모두 요청에 포함될 때 XFCC 헤더를 전달합니다.                                  |
   | - 로드 밸런서는 TCP를 통해 HAProxy의 인스턴스로 TLS handshake를 통과하도록 구성되어 있습니다.<br> - HA Proxy 인스턴스 수가 0 보다 많습니다.         | HA Proxy                     | HA Proxy는 TLS handshake에서 받은 클라이언트 인증서로 XFCC 헤더를 설정합니다. Gorouter는 헤더를 전달합니다.    |
   | - 로드 밸런서는 TCP를 통해 TLS 핸드셰이크를 통해 Gorouter의 인스턴스로 전달하도록 구성됩니다.                                               | Gorouter                     | Gorouter는 요청에 포함된 XFCC 헤더를 제거하고 TLS handshake에서 받은 클라이언트 인증서를 새 XFCC 헤더로 전달합니다. |

10. **HAProxy behavior for client certificate validation** : 
    
    -  HAProxy does not request client certificates : TLS termination point는 HA Proxy와 호환되지 않습니다. HAProxy는 클라이언트 인증서를 요청하지 않으므로 클라이언트가 인증서를 제공하지 않으며 유효성 검사가 발생하지 않습니다.
    
    -  HAProxy requests but does not require client certificates : HAProxy는 TLS handshake에서 클라이언트 인증서를 요청하고 제시될 때 유효성을 검사하지만 요구하지 않습니다. 

11. (선택)**Certificate Authorities trusted by the HAProxy** : 
    
    - 추가필요

12. **Gorouter behavior for client certificate validation** :
    
    - The Gorouter does not request client certificates : 클라이언트 인증서가 요청되지 않으므로 클라이언트가 이를 제공하지 않으며 클라이언트 인증서의 유효성 검사가 발생하지 않습니다.
    
    -  The Gorouter requests but does not require client certificates : Gorouter는 TLS handshake에서 클라이언트 인증서를 요청하고 제시될 때 유효성을 검사하지만 요구하지는 않습니다.
    
    - The Gorouter requires client certificates : Gorouter는 클라이언트 인증서가 Gorouter가 신뢰하는 인증 기관에 의해 서명되었는지 확인합니다.

13. **TLS cipher suites for the Gorouter** : Gorouter와 frontend 클라이언트(ex. 로드밸런서 또는 HAProxy)간의 TLS handshake에 대한 TLS 암호화 제품군을 검토합니다. 

14. **TLS cipher suites for HAProxy** : HAProxy와 frontend 클라이언트(ex. 로드밸런서 및 Gorouter)간의 TLS handshake에 대한 TLS 암호화 제품군을 검토합니다.

15. **HAProxy forwards all requests to the Gorouter over TLS** : HAProxy가 TLS를 통해 모든 요청을 Gorouter로 전달여부를 선택합니다.
    
    - Enable : Certificate authority for HAProxy back end 필드에 Gorouter 및 HAProxy에 대한 인증서 및 개인 키 필드에서 구성한 인증서에 서명한 CA를 제공합니다. 
    
    - Disable : Resource Config 창에서 HAProxy job 인스턴스 수를 0으로 설정합니다.

16. **HAProxy support for HSTS** : HAProxy에 요청할 때 브라우저에서 강제로 HTTPS를 사용 여부를 선택합니다.
    
    - Enable : HSTS요청에 대한 최대 기간(초)를 설정합니다. 하위 도메인 포함 확인란을 활성화 하여 브라우저가 모든 구성 요소 하위 도메인에 대해 HTTPS 요청을 사용하도록합니다. 확인란을 선택하여 HAProxy에 액세스하는 Google Chrome, Firefox 및 Safari의 인스턴스가 HTTPS가 필요한 알려진 호스트의 내장 목록을 참조하도록 강제합니다.
    
    - Disable : SSL 암호화를 사용하지 않거나 자체 서명된 인증서를 사용하는 경우 해당 칸을 선택합니다.

17. (선택)**Disable HTTP on the Gorouter and HAProxy** : Gorouter 또는 HAProxy가 HTTP 트래픽을 거부하도록 하려면 해당 칸을 선택합니다.

18. (선택)**Disable insecure cookies on the Gorouter** : Gorouter에서 생성된 쿠키에 대한 보안 플래그를 켜려면 해당 칸을 선택합니다.

19. (선택)**Enable Zipkin tracing headers on the Gorouter** : Zipkin 추적 헤더는 기본적으로 활성화되어 있습니다. 비활성화 하기 위해서는 해당 칸을 선택합니다.

20. (선택)**Enable the Gorouter to write access logs locally** : 기본적으로 선택되어 있습니다. VMware는 로그가 충분히 빠르게 회전되지 않고 디스크를 가득 채울 수 있도록 트래픽이 많은 배포의 경우 이 확인란을 선택 취소할 것을 권장합니다.

21. (선택)**Gorouters reject requests for isolation segments** : 기본적으로 TAS Gorouter는 Isolation segment 타일에서 생성된 Isolation segment에 배포된 앱의 트래픽을 처리합니다. 각 Isolation segment에 대해 Gorouter를 배포하지 않으려면 이 옵션을 활성화하지 않습니다. 

22. (선택)**Enable support for PROXY protocol in the Gorouter** : 기본적으로 PROXY 프로토콜에 대한 Gorouter 지원은 비활성화되어 있습니다. PROXY 프로토콜을 활성화하려면 해당 칸을 선택합니다.

23. **Route services** : Route 서비스는 앱 요청 및 응답에 대해 필터링 또는 콘텐츠 변환을 수행하는 서비스 입니다. 

24. (선택)**Maximum connections per back end** : 백엔드에 대한 앱 연결 수를 제한하려면 백엔드당 최대 연결 값을 입력합니다. 기본값은 500입니다.

25. **Keep-alive connections for the Gorouter** : 활성화 비활성화를 선택할 수 있습니다. Keep-alive 연결은 기본적으로 활성화 되어있습니다.

26. (선택)**Back end request timeout and idle timeout for the Gorouter** : 대기 시간이 긴 연결을 통해 더 큰 업로드를 수용하려면 백엔드에 대한 Gorouter 제한 시간 필드에서 초 수를 늘립니다.

27. (선택)**Front end idle timeout for the Gorouter and HAProxy** : 프런트엔드 유휴제한 시간을 사용하여 로드 밸런서에서 Gorouter 또는 HAProxy로의 연결이 조기에 닫히지 않도록 합니다.

28. (선택)**Gorouter drain timeout** : Gorouter가 종료되기 전에 기존 연결을 계속 지속하도록 합니다. 기본값은 900입니다.

29. (선택)**Load balancer unhealthy threshold** : 해당 값을 늘려 Gorouter가 종료되기 전에 연결을 계속 수락하는 시간을 초 단위로 지정합니다. 이 기간 동안 상태 확인은 Gorouter를 비정상으로 보고할 수 있습니다.

30. (선택)**Load balancer healthy threshold** : 이 필드는 Gorouter 인스턴스가 시작되었다고 선언할 때 까지 대기하는 시간을 초 단위로 지정합니다. 

31. (선택)**HTTP headers to log** : 개발자가 HTTP 요청의 특정 HTTP 헤더를 Gorouter의 정보와 함께 앱 로그에 표시하려는 경우 기록합니다.






