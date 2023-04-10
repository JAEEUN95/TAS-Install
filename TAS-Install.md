# 설치 방법

## TAS 설치

#### VMware Tanzu Application Service

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

| HAProxy 사용여부 | Gorouter IP 필드 | HAProxy IP 필드 |
| ------------ | -------------- | ------------- |
|              |                |               |

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

32. **HAProxy request maximum buffer size** : 기본 최대값인 16.384KB보다 큰 요청이 예상되는 경우 HAProxy 요청 최대 버퍼 크기에 새 값(바이트)을 입력하십시오.

33. TAS에 HAProxy를 사용하고 특정 소스에만 트래픽을 수신하도록 하려면 다음 필드를 구성합니다.
    
    - **HAProxy protected domains** : 알 수 없는 소스 요청으로부터 보호하기 위해 쉼표로 구분된 도메인 목록을 입력합니다. 
    
    - **HAProxy trusted CIDRs** : 공백으로 구분된 CIDR 목록을 입력하여 보호된 도메인의 IP 주소가 TAS로 트래픽을 보낼 수 있도록 제한합니다.

34. **Loggregator port** : 공백으로 비어있다면 443으로 설정됩니다. 

35. **Container network interface plugin** : 다음 옵션 중 하나를 선택합니다.
    
    - Silk : 이 옵션은 TAS의 기본 컨테이너 네트워크 인터페이스(CNI)입니다.
      
      - App network maximum transmission unit : MTU 값을 바이트 단위로 입력합니다. 기본값은 1454 입니다.
      
      - (선택)Overlay subnet : 오버레이 네트워크 IP 범위를 입력합니다. 범위를 설정하지 않으면 10.255.0.0/16 입니다.
      
      - VXLAN tunnel endpoint port : UDP 포트 번호를 입력합니다. VXLAN패킷을 수신하는 호스트 포트입니다. 사용자 지정 설정하지 않으면 4789를 사용합니다.
      
      - Denied logging interval : 컨테이너별 네트워킹 정책 또는 ORG, SPACE 또는 배포에 적용되는 ASG(App Security Group) 규칙에 의해 차단된 패킷에 대한 초당 속도 제한을 설정합니다. 이 필드의 기본값은 1입니다.
      
      - UDP logging interval : 전송 및 수신되는 UDP 패킷에 대한 초당 속도 제한을 설정합니다. 이 필드의 기본값은 100 입니다. 
      
      - Log traffic for all accepted and denied app packets : 앱 트래픽에 대한 로깅을 활성화하려면 확인란을 활성화 합니다. 선택 시 로그양이 증가합니다.
      
      - Enable Silk network policy enforcement : 앱 간의 Silk 네트워크 정책 적용을 비활성화 하려면 확인란을 해제합니다. 기본적으로 선택되어 있습니다. 이 기능을 비활성화하면 모든 앱 컨테이너가 제한 없이 다른 모든 앱 컨테이너를 액세스할 수 있습니다.
      
      - Enable dynamic application security group changes : 컨테이너 재시작 없이 ASG를 앱에 적용할 수 있습니다. 
    
    - External : VMware NSX-T Container Plug-in을 배포하는 경우에 선택합니다.

36. **DNS search domains** : DNS 검색 도메인으로 사용할 앱 컨테이너의 도메인을 입력합니다.

37. **Database connection timeout** : 정책 서버 및 Silk 데이터베이스의 클라이언트에 대한 연결 제한 시간을 초 단위로 설정합니다. 기본값은 120입니다. 배포에서 컨테이너 간 네트워킹과 관련된 시간 초과 문제가 발생하는 경우 이 값을 늘려야 할 수 있습니다.

38. **TCP routing** : TCP 라우팅은 기본적으로 비활성화되어 있습니다. DNS가 TCP 라우터에 직접 전달되지 않고 로드 밸런서를 통해 TCP 트래픽을 보내는 경우 이 기능을 활성화해야 합니다.

39. (선택)**Remove specified HTTP response headers** : Gorouter가 앱 응답에서 제거할 헤더를 입력합니다.

40. (선택)**Sticky session cookies** : 하나 이상의 고정 세션 쿠키 이름을 입력합니다. 기본 쿠키 이름은 JSESSIONID 입니다. 일부 앱에는 다른 쿠키 이름이 필요합니다. 예를 들어 Spring WebFlux에는 SESSION 이름이 필요합니다. Gorouter는 이러한 쿠키를 사용하여 세션 선호도 또는 고정 세션을 지원합니다.



##### 모든 설정 입력 후 Save를 클릭합니다.



- **App Containers**

![](appcontainers01.png)

1. **Enable custom buildpacks** : cf push -b 명령 옵션 사용자 지정 빌드팩 URL을 전달하는 기능을 제어합니다. 이 기능은 기본적으로 활성화 되어 있어 개발자가 앱을 배포할 때 사용자 정의 빌드팩을 사용할 수 있습니다.

2. **Allow SSH access to app containers** : 앱 인스턴스에 대한 SSH 액세스를 제어합니다.

3. **Enable SSH when an app is created** : 기본적으로 새 앱에 대한 SSH 액세스를 활성화하려면 앱 생성 시 SSH 활성화 확인란을 선택합니다.

4. **Gorouter app identity verification** : Gorouter가 암호화를 활성화하고 잘못된 라우팅을 방지하기 위해 앱 ID를 확인하는 방법을 선택합니다.
   
   - The Gorouter uses TLS to verify app identity : Gorouter가 TLS를 사용하여 앱 ID를 확인할 수 있도록 합니다. 이것이 기본 옵션입니다.
   
   - The Gorouter and apps use mutual TLS to verify each other's identity : 이 옵션은 앱 컨테이너가 Gorouter에서 들어오는 통신만 수락하기 때문에 TCP 라우팅을 비활성화합니다.

5. **Private Docker insecure registry allow list** : 쉼표로 구분된 IP 주소 범위 목록을 제공하여 Docker 컨테이너에서 앱 인스턴스를 실행하도록 VM용 TAS를 구성할 수 있습니다.

6. **Docker images disk cleanup scheduling on Diego Cell VMs** : 디스크 사용량을 채워 디스크 공간 정리를 선택한 경우 Reserved disk space for other jobs에 MB 단위로 값을 입력합니다.

7. **Enable containerd delegation** : Garden이 컨테이너 생성 및 삭제 작업을 containerd 도구에 위임하는지 여부를 결정합니다. 기본적으로 이 옵션은 활성화되어 있으며 Garden은 containerd를 사용합니다.

8. **Max-in-flight container starts** : 이 숫자는 배포 시 디에고 셀에서 시작되는 최대 인스턴스 수를 구성합니다. 0은 제한이 없음을 의미합니다.

9. **NFSv3 volume services** : NFS 볼륨 서비스를 통해 앱 개발자는 공유 파일 액세스를 위해 기존 NFS 볼륨을 앱에 바인딩할 수 있습니다. (선택)NFSv3 볼륨 서비스에 대해 LDAP를 구성하려면 다음과 같이 설정합니다.
   
   - LDAP service account user : 볼륨 서비스를 관리할 LDAP 서비스 계정의 사용자 이름을 입력합니다.
   
   - LDAP service account password : 서비스 계정의 비밀번호를 입력합니다.
   
   - LDAP server host : LDAP 서버의 호스트 이름 또는 IP 주소를 입력합니다.
   
   - LDAP server port :  LDAP 서버 포트 번호를 입력합니다. 기본값은 389입니다.
   
   - LDAP user search base : LDAP 사용자 검색이 시작되는 LDAP 디렉토리 트리의 위치를 ​​입력합니다. 일반적인 LDAP 검색 기반은 도메인 이름과 일치합니다.
   
   - LDAP server CA certificate :  LDAP 서버가 TLS를 지원하고 NFS 드라이버에서 LDAP 서버로의 TLS 연결을 활성화하려는 경우 선택적으로 인증서를 입력할 수 있습니다.

10. **Enable SMB volume services** : SMB 볼륨 서비스를 활성화하면 개발자가 기존 SMB 공유를 앱에 바인딩할 수 있습니다. SMB 볼륨을 활성화하는 경우 Errands창에서 SMB Broker Errand를 On으로 설정합니다.

11. **Default health check timeout. Enter value in seconds** : 이 필드에 구성된 값은 앱 시작과 앱의 첫 번째 정상 응답 사이에 허용되는 시간입니다. 상태 확인이 구성된 제한 시간 내에 정상 응답을 받지 못하면 앱이 비정상으로 선언됩니다. 기본 제한 시간은 `60`초이고 구성 가능한 최대 제한 시간은 `600`초입니다.

12. (선택)**App log rate limit (beta)** : 앱 인스턴스가 초당 생성할 수 있는 로그 라인 수를 제한하려면 정수 값을 입력합니다.



##### 모든 설정 입력 후 Save를 클릭합니다.



- **App Developer Controls**

![](appdevelopercontrols01.png)

1. **Maximum file upload size** : 빌드팩을 포함한 앱 업로드의 최대 크기를 MB 단위로 입력합니다.

2. **Maximum package size** : 앱 파일의 최대 총 크기(MB)입니다.

3. **Default app memory** : 지정된 값이 없는 경우 새로 배포된 앱에 할당된 기본 메모리 양(MB)입니다.

4. **Default app memory quota per org** : ORG의 모든 앱에 대한 기본 메모리 제한입니다. 초기 설치 후 운영자가 cf CLI를 사용하여 기본값을 변경할 수 있습니다.

5. **Maximum disk quota per app** : 앱당 허용되는 최대 디스크 양입니다. 디스크 용량이 부족하면 큰 앱이 성공적으로 배포되지 않을 수 있습니다.

6. **Default disk quota per app** : 새로 배포된 앱에 기본적으로 할당된 디스크의 양입니다.

7. **Default service instance quota per org** : ORG 당 기본 서비스 인스턴스 할당량을 입력합니다. 초기 설치 후 운영자가 cf CLI를 사용하여 기본값을 변경할 수 있습니다.

8. **Staging timeout** : 클라우드 컨트롤러로 앱 droplet을 준비하면 이 필드에 지정한 시간(초) 후에 서버가 시간 초과됩니다.

9. **Internal domains** : 앱이 내부 DNS 서비스 검색에 사용하는 도메인을 하나 이상 입력합니다. 이 값의 기본값은 `apps.internal`입니다.

10. **Allow space developers to manage network policies** : 확인란을 활성화하여 개발자가 자신의 앱에 대한 자체 네트워크 정책을 관리하도록 허용할 수 있습니다.



##### 모든 설정 입력 후 Save를 클릭합니다.



- **App Security Groups**

![](appsecuritygroups01.png)

1. **Type "X" to acknowledge this requirement** : 안전한 앱 배포를 위해서는 적절한 앱 보안 그룹을 설정하는것이 중요합니다. TAS for VMs 배포가 완료된 후 적절한 앱 보안 그룹 설정에 대한 책임이 귀하에게 있음을 인정하려면  `X` 를 입력합니다.



##### 모든 설정 입력 후 Save를 클릭합니다.



- **Authentication and Enterprise SSO** 

![](sso01.png)

1. **Configure your UAA user account store with either internal or external authentication mechanisms** 
   
   -  Internal UAA : 내부 UAA를 선택하고 절차에 따라 비밀번호 정책을 구성하십시오.
   
   - SAML identity provider : SAML을 통해 외부 ID 공급자에 연결하려면 선택 후 구성하십시오.
   
   - LDAP server : 외부 LDAP 서버에 연결하려면 선택 후 구성하십시오.



##### 모든 설정 입력 후 Save를 클릭합니다.






























