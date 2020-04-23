## 관련자료

 - PDF 교재: 
https://github.com/event-storming/docs/blob/master/__%E1%84%80%E1%85%AD%E1%84%8C%E1%85%A2-Azure-pipeline.pdf
- 실습스크립트 룩업:
https://workflowy.com/s/msa/27a0ioMCzlpV04Ib

- 페이스북 그룹 : 
https://www.facebook.com/groups/cloudswmoding  

- 이벤트 스토밍 코드 전환 : 
http://msaez.io/  

- 구글 클라우드 : 
https://cloud.google.com/  

- Quick Tour 실습 난이도 조사  :
https://docs.google.com/forms/d/e/1FAIpQLSe0nb4_C8myV23BNgqRevQvoNCEP6OSIlxDtVfRgcunxW39Cw/viewform

## 참고도서

- Overall MSA Design patterns: 
    https://www.manning.com/books/microservices-patterns

- Microservice decomposition strategy:
   - DDD distilled: https://www.oreilly.com/library/view/domain-driven-design-distilled/9780134434964/
   - Event Storming: https://leanpub.com/introducing_eventstorming

- Database Design in MSA: 
   - Lightly: https://www.confluent.io/wp-content/uploads/2016/08/Making_Sense_of_Stream_Processing_Confluent_1.pdf
   - Deep dive: https://dataintensive.net/?fbclid=IwAR3OSWkhqRjLI9gBoMpbsk-QGxeLpTYVXIJVCSaw_A5eYrBDc0piKSm4pMM

- API design and REST: 
    https://pepa.holla.cz/wp-content/uploads/2016/01/REST-in-Practice.pdf


## 카프카 설치
- 다운로드  
https://kafka.apache.org/downloads  ==> 최근 버전의 Scala 2.12 (binary)버전으로 다운로드  

## eventTopic 이라는 Topic 에 메세지 통신
- 토픽 생성  
kafka-topics.bat --zookeeper localhost:2181 --topic eventTopic --create --partitions 1 --replication-factor 1  

kubectl -n kafka exec my-kafka-0 -- /usr/bin/kafka-topics --zookeeper my-kafka-zookeeper:2181 --topic eventTopic --create --partitions 1 --replication-factor 1


- 토픽 리스트 보기  
kafka-topics.bat --zookeeper localhost:2181 --list  

kubectl -n kafka exec my-kafka-0 -- /usr/bin/kafka-topics --zookeeper my-kafka-zookeeper:2181 --list

- 이벤트 발행하기  

---- window   
bin/windows/kafka-console-producer.bat --broker-list http://localhost:9092 --topic eventTopic

---- mac  
bin/kafka-console-producer --broker-list http://localhost:9092 --topic eventTopic

--- cluster  
kubectl -n kafka exec -ti my-kafka-0 -- /usr/bin/kafka-console-producer --broker-list my-kafka:9092 --topic eventTopic


- 이벤트 수신하기

---- window   
bin/windows/kafka-console-consumer.bat --bootstrap-server http://localhost:9092 --topic eventTopic --from-beginning


---- mac  
bin/kafka-console-consumer.sh --bootstrap-server http://localhost:9092 --topic eventTopic --from-beginning


---- cluster  
kubectl -n kafka exec -ti my-kafka-0 -- /usr/bin/kafka-console-consumer --bootstrap-server my-kafka:9092 --topic eventTopic --from-beginning


## 카프카 서버의 실행 (필요한 경우만)
---- window   
- (새창)주키퍼 실행  
bin/windows/zookeeper-server-start.bat config/zookeeper.properties  
or  
cd bin/window   
zookeeper-server-start.bat ../../config/zookeeper.properties

- (새창)카프카 실행  
bin/windows/kafka-server-start.bat config/server.properties  
or  
cd bin/window  
kafka-server-start.bat ../../config/server.properties

---- mac  (brew 로 설치시)
- 주키퍼 실행  
zkserver start  
- 카프카 실행  
/usr/local/bin/kafka-server-start /usr/local/etc/kafka/server.properties

## 이벤트 발송 시나리오


주문이 발생함 – order  
주문이 발생함 - 상품 수량 변경됨 - product  
주문이 발생함 - 배송 시작됨 – delivery  
배송 시작됨 - 배송 완료됨 – delivery  
배송 완료됨 - 상품추천 – marketing  
설문조사 - 블랙리스트 추가 - service_center  
블랙리스트 추가 - mypage


주문 취소함 - order  
주문 취소함 - 상품 수량 변경됨 - product  
주문이 취소함 - 배송 취소됨 – delivery  


## 1번 시나리오 - ui 없이 order, product, delivery, marketing, service_center 프로젝트를 실행하고 주문이벤트 발생

```
#### product 서비스 실행시 발생함 - 상품이 입력됨
{"eventType":"ProductChanged","timestamp":"20190905150002","stateMessage":"상품 변경이 발생함","productId":1,"productName":"TV","productPrice":10000,"productStock":10,"imageUrl":"https://github.githubassets.com/images/modules/profile/profile-joined-github.png"}
{"eventType":"ProductChanged","timestamp":"20190905150002","stateMessage":"상품 변경이 발생함","productId":2,"productName":"RADIO","productPrice":20000,"productStock":20,"imageUrl":"https://github.githubassets.com/images/modules/profile/profile-joined-github.png"}
{"eventType":"ProductChanged","timestamp":"20190905150002","stateMessage":"상품 변경이 발생함","productId":3,"productName":"NOTEBOOK","productPrice":30000,"productStock":30,"imageUrl":"https://github.githubassets.com/images/modules/profile/profile-joined-github.png"}
{"eventType":"ProductChanged","timestamp":"20190905150002","stateMessage":"상품 변경이 발생함","productId":4,"productName":"TABLE","productPrice":40000,"productStock":40,"imageUrl":"https://github.githubassets.com/images/modules/profile/profile-joined-github.png"}
{"eventType":"ProductChanged","timestamp":"20190905150002","stateMessage":"상품 변경이 발생함","productId":5,"productName":"CLOCK","productPrice":50000,"productStock":50,"imageUrl":"https://github.githubassets.com/images/modules/profile/profile-joined-github.png"}

#### http localhost:8081/orders productId=1 quantity=3 customerId="1@uengine.org" customerName="홍길동" customerAddr="서울시" 입력시 발생함
{"eventType":"OrderPlaced","timestamp":"20190916151922","stateMessage":"주문이 발생함","productId":2,"orderId":3,"productName":"RADIO","quantity":3,"price":20000,"customerId":"1@uengine.org","customerName":"홍길동","customerAddr":"서울시"}
{"eventType":"DeliveryStarted","timestamp":"20190916151922","stateMessage":"배송이 시작됨","deliveryId":3,"orderId":3,"customerId":"1@uengine.org","customerName":"홍길동","deliveryAddress":"서울시","deliveryState":"DeliveryStarted"}
{"eventType":"ProductChanged","timestamp":"20190916151922","stateMessage":"상품 변경이 발생함","productId":2,"productName":"RADIO","productPrice":20000,"productStock":14,"imageUrl":"/goods/img/RADIO.jpg"}
{"eventType":"DeliveryCompleted","timestamp":"20190916151922","stateMessage":"배송이 완료됨","deliveryId":3,"orderId":3,"customerId":"1@uengine.org","customerName":"홍길동","deliveryAddress":"서울시","deliveryState":"DeliveryCompleted"}

#### http http://localhost:8084/surveys customerId="1@uengine.org" surveyMessage="nonooooo" productSatisfaction=1 입력시 발생함
{"eventType":"SurveyCompleted","timestamp":"20190905150424","stateMessage":"설문이 완료됨","customerName":"1@uengine.org","surveyMessage":"nonooooo","productSatisfaction":1}
{"eventType":"BlackListAdded","timestamp":"20190905150424","stateMessage":"블랙리스트로 추가됨","customerId":"1@uengine.org"}

```
## 2번 시나리오 mypage 를 실행 후에, 발생된 이벤트를 모두 받아서 조회를 함
mypage 는 CQRS 모형이며, 그중 READ 부분을 담당한다.  
각종 일어난 이벤트들을 받아서, 자신만의 DB 에 저장을 하여 보여주는 서비스이다.  
> mypage 의 정보는 DB 에 저장을 하여서.. orderid 부분이 안맞을수도 있습니다.

```
### 유저 정보 조회 -> http http://localhost:8086/users/1@uengine.org  
{
    "_links": {
        "self": {
            "href": "http://localhost:8086/users/1@uengine.org"
        },
        "user": {
            "href": "http://localhost:8086/users/1@uengine.org"
        }
    },
    "accountNonExpired": true,
    "accountNonLocked": true,
    "address": "서울시 논현동",
    "credentialsNonExpired": true,
    "enabled": true,
    "money": 900000,
    "nickname": "유엔진",
    "role": null
}

###  주문 히스토리 조회 -> http http://localhost:8086/mypage/order/1@uengine.org  
[
    {
        "deliveryCompleted": true,
        "deliveryId": 3,
        "deliveryStarted": true,
        "nickName": "홍길동",
        "orderId": 3,
        "payment": 60000,
        "productId": 2,
        "productName": "RADIO",
        "quantity": 3,
        "timestamp": "20190916151922",
        "userId": "1@uengine.org"
    }
]

```

## 3번 시나리오 ui, gateway, oauth 서버를 띄운후에 화면으로 조회
http://localhost:8080 



## 각 팀별로 event-storming 에 있는 프로젝트를 fork

- 상품개발팀 같은 경우 event-storming/product 프로젝트에 들어가서 오른쪽 상단의 Fork 버튼 클릭

- IDE 환경에서 해당 git 소스코드를 clone 받아서 개발 함

```
git clone https://github.com/event-storming/[username]/products.git
```

## httpie 설치

https://github.com/TheOpenCloudEngine/uEngine-cloud/wiki/Httpie-%EC%84%A4%EC%B9%98

환경변수 등록: windows10 Windows+R 창에서 **sysdm.cpl ,3** 입력 후 설정

Python 설치 후 httpie 설치 시, SSL Certificate Error 우회 방안 : 
 - pip install --trusted-host files.pythonhosted.org httpie
 

## 템플릿 프로젝트는 주문이 발생하였을때 연관되어서 이벤트가 발생하도록 설계됨
-- 주문 발생  
http localhost:8081/orders productId=1 quantity=3 customerId="1@uengine.org" customerName="홍길동" customerAddr="서울시"  
-- 주문 취소  
http PATCH localhost:8081/orders/1 state=OrderCancelled   

>> order 서비스를 실행할때 product 서비스에서 request 방식으로 재고를 체크하고 싶을때는  
>> mvn spring-boot:run -DcheckStock=true  
>> 로 order 서비스를 실행하면 됩니다.  


)))))))))))))))))))))))))))))
Pipeline 설정

portal.azure.com에서 클러스터 / 레지스터리 생성

dev.azure.com 로그인 (애저 계정으로)
프로젝트 생성 -> 왼쪽 메뉴 활성화
Pipelines -> library 에서  Variable group name 을 common-value로 설정




서버 카프카 설치
- az aks get-credentials --resource-group admin25 --name admin25aks
- az aks update –n admin25aks –g admin25 --attach-acr admin25acr
- kubectl --namespace kube-system create sa tiller     
- kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
- helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
- helm repo update
- kubectl create ns kafka
- helm install my-kafka --namespace kafka incubator/kafka
- 확인 방법
   kubectl get po -n kafka
. yaml에서 Variable group = common-value 로 수정 필요
Pipeline > environments  create environment  클릭 

    Name = aks?? 
    Kubernetes  선택 
    클러스터, name space 선택
위 값은 azure-pipelines.yml의 값과 일치

