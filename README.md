# 주제 : OSS (Operation Support System)

# 서비스 시나리오
1. 기능적 요구사항
  - 청약자가 서비스 신규가입화면에서 인터넷과 TV 상품을 청약한다
  - 청약을 하면 NE(Network Element) 장비로 가입자 정보를 Interface하는 Activation 작업을 수행한다
  - Activation 작업이 완료되면 Scheduling 을 작업을 요청한다
  - Scheduler는 개통요청된 작업오더에 대해 고객에게 Outbound TM을 통해 방문시간을 정한 후 작업자할당을 한다
  - 작업자는 본인에게 할당된 오더에 대해 작업을 완료 후 작업결과를 입력한다. 
  - 현장의 상황에 따라 개통을 할 수 없는 경우 반송처리를 하며 반송할 경우 Activation 은 원복되어야 한다
  - 청약자는 전체적인 개통처리 현황을 확인할 수 있어야 한다. 
2. 비기능적 요구사항
  - 트랜잭션
    스케줄링 호출이 되지 않으면, Activation 상태는 그대로 유지가 된다. Sync 호출
  - 장애격리
    Activation 기능이 수행되지 않더라도 청약 접수는 365일 24시간 진행 될 수 있어야 한다. Async (event-driven), Eventual Consistency
    스케줄링 서비스의 부하가 과중되면 호출을 잠시동안 받지 않고 잠시후에 하도록 유도한다 Circuit breaker, fallback
  - 성능
    청약자는 개통처리 상황을 마이페이지(프론트엔드)에서 확인할 수 있어야 한다 CQRS

# 모델링
경로: http://www.msaez.io/#/storming/cr2xPwrCuogB3RmUnLsg1ghhJpI2/mine/9497f301cf8f22b00a7710f3510709aa/-MIb0RpRdZ8yv5M0TByS

<img width="1269" alt="MSA_OSS" src="https://user-images.githubusercontent.com/22510081/96206131-38aed580-0fa3-11eb-81fc-fc8470b9e104.png">


# 구현

분석/설계 단계에서 도출된 헥사고날 아키텍처에 따라, 각 BC별로 대변되는 마이크로 서비스들을 스프링부트로 구현하였다. 
- oss-order : 8081
- oss-activation : 8082
- oss-scheduling : 8083
- oss-fieldwork : 8084
- oss-gateway : 8088 (local), 8080 (docker)

1. IntelliJ 프로젝트 구성
<img width="1280" alt="IntelliJ_Project" src="https://user-images.githubusercontent.com/22510081/96367999-10ce9600-118c-11eb-8867-d338145c812b.png">
2. GitHub Repository 구성
<img width="1280" alt="GitHub_Repository" src="https://user-images.githubusercontent.com/22510081/96368002-14621d00-118c-11eb-882e-37aa69209812.png">

# 업무처리 흐름
1. Local Scenario 1 (청약 후 처리완료)
<img width="1214" alt="flow1-1" src="https://user-images.githubusercontent.com/22510081/96368169-68b9cc80-118d-11eb-9f89-0779028cf5eb.png">
<img width="1214" alt="flow1-1_kafka" src="https://user-images.githubusercontent.com/22510081/96368170-68b9cc80-118d-11eb-8c13-614ec15a0d37.png">
<img width="1214" alt="flow1-2" src="https://user-images.githubusercontent.com/22510081/96368171-69526300-118d-11eb-9aa5-26f3468ccb89.png">
<img width="1214" alt="flow1-2_kafka" src="https://user-images.githubusercontent.com/22510081/96368173-69526300-118d-11eb-9087-02c46e60fdb4.png">
<img width="1214" alt="flow1-2_myPage" src="https://user-images.githubusercontent.com/22510081/96368174-69eaf980-118d-11eb-9c09-83c82d013737.png">
<img width="1214" alt="flow1-3" src="https://user-images.githubusercontent.com/22510081/96368166-66f00900-118d-11eb-84b3-58845cff0cf7.png">
<img width="1214" alt="flow1-3_kafka" src="https://user-images.githubusercontent.com/22510081/96368167-67889f80-118d-11eb-8de6-27e7c9d59ddc.png">
<img width="1214" alt="flow1-3_myPage" src="https://user-images.githubusercontent.com/22510081/96368168-68213600-118d-11eb-8369-efefc6cd21f7.png">

2. Local Scenario 2 (청약 후 반송)
<img width="1214" alt="flow2-1" src="https://user-images.githubusercontent.com/22510081/96368250-f5648a80-118d-11eb-908c-8ed82233341f.png">
<img width="1214" alt="flow2-1_kafka" src="https://user-images.githubusercontent.com/22510081/96368251-f5648a80-118d-11eb-963d-74de40d5e3fa.png">
<img width="1214" alt="flow2-2" src="https://user-images.githubusercontent.com/22510081/96368253-f5fd2100-118d-11eb-8942-568992f26144.png">
<img width="1214" alt="flow2-2_kafka" src="https://user-images.githubusercontent.com/22510081/96368255-f5fd2100-118d-11eb-88ec-d52866f26abf.png">
<img width="1214" alt="flow2-2_myPage" src="https://user-images.githubusercontent.com/22510081/96368243-f3023080-118d-11eb-8dda-62206c403a9b.png">
<img width="1214" alt="flow2-3" src="https://user-images.githubusercontent.com/22510081/96368244-f39ac700-118d-11eb-88b2-3d827b69adef.png">
<img width="1214" alt="flow2-3_kafka" src="https://user-images.githubusercontent.com/22510081/96368245-f4335d80-118d-11eb-9fc1-2f16d3d5d842.png">
<img width="1214" alt="flow2-3_myPage" src="https://user-images.githubusercontent.com/22510081/96368290-2b097380-118e-11eb-84e8-f7ef7a6c00be.png">
<img width="1214" alt="flow2-3_saga" src="https://user-images.githubusercontent.com/22510081/96368249-f4cbf400-118d-11eb-99f5-fe16e5b94dbf.png">

3. Gateway Scenario (청약 후 청약취소)
<img width="1214" alt="gateway-flow1-1" src="https://user-images.githubusercontent.com/22510081/96368330-512f1380-118e-11eb-977a-df4ced41db07.png">
<img width="1214" alt="gateway-flow1-1_kafka" src="https://user-images.githubusercontent.com/22510081/96368332-51c7aa00-118e-11eb-86a5-fed3172cc0c6.png">
<img width="1214" alt="gateway-flow1-2" src="https://user-images.githubusercontent.com/22510081/96368333-51c7aa00-118e-11eb-97fd-fd06267a6e43.png">
<img width="1214" alt="gateway-flow1-2_kafka" src="https://user-images.githubusercontent.com/22510081/96368334-52604080-118e-11eb-8dfb-2f7e5ab485f6.png">
<img width="1214" alt="gateway-flow1-2_myPage" src="https://user-images.githubusercontent.com/22510081/96368335-52604080-118e-11eb-956f-a74dceac412e.png">
<img width="1214" alt="gateway-flow1-3" src="https://user-images.githubusercontent.com/22510081/96368337-52f8d700-118e-11eb-9b28-c4e20e470fef.png">
<img width="1214" alt="gateway-flow1-3_kafka" src="https://user-images.githubusercontent.com/22510081/96368325-4eccb980-118e-11eb-9b1d-8a08ade60d46.png">
<img width="1214" alt="gateway-flow1-3_myPage" src="https://user-images.githubusercontent.com/22510081/96368326-4ffde680-118e-11eb-853e-20c8ef0fff14.png">
<img width="1214" alt="gateway-flow1-4" src="https://user-images.githubusercontent.com/22510081/96368327-50967d00-118e-11eb-839e-a171a958b419.png">
<img width="1214" alt="gateway-flow1-4_kafka" src="https://user-images.githubusercontent.com/22510081/96368328-50967d00-118e-11eb-9aed-0d31fe573995.png">
<img width="1214" alt="gateway-flow1-4_myPage" src="https://user-images.githubusercontent.com/22510081/96368329-512f1380-118e-11eb-9261-f4bf3111f0b3.png">

# 평가항목
1. Saga
- Eventual TX: 고객이 주문이후 취소할 수 있음. Activation 이 완료된 이후 현장작업 중 반송을 하는 경우 이미 수행한 Activation 을 취소시킨다. 
- FeildWorkFailed Event -> RevertActivation Policy

2. CQRS
- MyPage 에서 CQRS 구현

public class MyPageViewHandler {

    @Autowired
    private MyPageRepository myPageRepository;

    @StreamListener(KafkaProcessor.INPUT)
    public void whenOrderPlaced_then_CREATE_1 (@Payload OrderPlaced orderPlaced) {
        
    }
    @StreamListener(KafkaProcessor.INPUT)
    public void whenActivationCompleted_then_UPDATE_1(@Payload ActivationCompleted activationCompleted) {

    }
    @StreamListener(KafkaProcessor.INPUT)
    public void whenScheduled_then_UPDATE_2(@Payload Scheduled scheduled) {

    }
    @StreamListener(KafkaProcessor.INPUT)
    public void whenFieldWorkCompleted_then_UPDATE_3(@Payload FieldWorkCompleted fieldWorkCompleted) {

    }
    @StreamListener(KafkaProcessor.INPUT)
    public void whenOrderCancelled_then_UPDATE_4(@Payload OrderCancelled orderCancelled) {

    }
    @StreamListener(KafkaProcessor.INPUT)
    public void whenActivationCancelled_then_UPDATE_5(@Payload ActivationCancelled activationCancelled) {

    }
    @StreamListener(KafkaProcessor.INPUT)
    public void whenScheduleCancelled_then_UPDATE_6(@Payload ScheduleCancelled scheduleCancelled) {

    }
    @StreamListener(KafkaProcessor.INPUT)
    public void whenFieldWorkFailed_then_UPDATE_7(@Payload FieldWorkFailed fieldWorkFailed) {

    }
}

<img width="1280" alt="CQRS1" src="https://user-images.githubusercontent.com/22510081/96368428-bf73d600-118e-11eb-9859-186f4d43d784.png">

3. Correlation
- OrderId 를 이용해 Correlation 을 맺음
<img width="1058" alt="Correlation1" src="https://user-images.githubusercontent.com/22510081/96368526-314c1f80-118f-11eb-862b-5fe099af4753.png">
<img width="1091" alt="Correlation2" src="https://user-images.githubusercontent.com/22510081/96368528-327d4c80-118f-11eb-9a13-978bc301e48b.png">

4. Request/Response
- Activation 완료 이후 Scheduling 서비스 호출시 Request/Rosponse 방식으로 구현함
<img width="1041" alt="ReqRes1" src="https://user-images.githubusercontent.com/22510081/96368566-580a5600-118f-11eb-8a3a-8358b23fd2fc.png">
<img width="1055" alt="ReqRes2" src="https://user-images.githubusercontent.com/22510081/96368567-593b8300-118f-11eb-8d12-0e71c5623aaf.png">

5. Gateway
- Sprint boot 의 gateway dependency 이용. 
- gateway flow 시연으로 갈음

6. Deploy/Pipeline
- Amazon CodeBuild 를 이용한 자동 빌드/배포
<img width="1231" alt="CodeBuild1" src="https://user-images.githubusercontent.com/22510081/96368616-a881b380-118f-11eb-9189-93d10d9d6414.png">

7. Circuit Break
- istio 가능하게 설정 변경
<img width="721" alt="CB1" src="https://user-images.githubusercontent.com/22510081/96408917-6607b780-121f-11eb-86c5-2dac0e4ed590.png">
- Deploy 재수행하여 sidecar 생성 확인
<img width="977" alt="CB2" src="https://user-images.githubusercontent.com/22510081/96408904-63a55d80-121f-11eb-8544-d03ee9abff6c.png">
- Destination Rule 생성
<img width="754" alt="CB3" src="https://user-images.githubusercontent.com/22510081/96408907-64d68a80-121f-11eb-8718-053f3f5e87be.png">
- Circuit Break 비적용 상태
<img width="538" alt="CB4-1" src="https://user-images.githubusercontent.com/22510081/96408909-64d68a80-121f-11eb-900e-347749f43a2e.png">
<img width="445" alt="CB4-2" src="https://user-images.githubusercontent.com/22510081/96408910-656f2100-121f-11eb-80fd-bbdb7abde73a.png">
- Circuit Break 적용 상태
<img width="563" alt="CB5-1" src="https://user-images.githubusercontent.com/22510081/96408911-656f2100-121f-11eb-90b5-b634a796dfc3.png">
<img width="571" alt="CB5-2" src="https://user-images.githubusercontent.com/22510081/96408913-6607b780-121f-11eb-8ed1-fedf26738412.png">


8. Autoscale(HPA)
<img width="494" alt="HPA1" src="https://user-images.githubusercontent.com/22510081/96405548-7bc5ae80-1218-11eb-9e77-2ffaa933f1ed.png">
<img width="644" alt="HPA2" src="https://user-images.githubusercontent.com/22510081/96405543-7a948180-1218-11eb-886d-0fab84bfbdf4.png">
<img width="1280" alt="HPA3" src="https://user-images.githubusercontent.com/22510081/96405545-7b2d1800-1218-11eb-891e-24f363014645.png">

9. Zero-downtime deploy (readiness probe)
<img width="1048" alt="ReadinessProbe" src="https://user-images.githubusercontent.com/22510081/96370757-e97ec580-1199-11eb-9513-834df99ea4f5.png">

10. ConfigMap/Persisteence Volume
- ConfigMap
<img width="1010" alt="ConfigMap1" src="https://user-images.githubusercontent.com/22510081/96370802-0ddaa200-119a-11eb-9395-cab6795aa0da.png">
<img width="1184" alt="ConfigMap2" src="https://user-images.githubusercontent.com/22510081/96370803-0f0bcf00-119a-11eb-9e29-218666f45f0e.png">
<img width="672" alt="ConfigMap1" src="https://user-images.githubusercontent.com/22510081/96409927-0e6a4b80-1221-11eb-8592-e780c4f2d060.png">

- Persistence Volume
<img width="941" alt="PVC1" src="https://user-images.githubusercontent.com/22510081/96370829-2cd93400-119a-11eb-88e8-f7b5c6344d3a.png">
<img width="623" alt="PVC_storageclass pvc생성" src="https://user-images.githubusercontent.com/22510081/96409837-e549bb00-1220-11eb-9660-64bfa1780a96.png">

11. Ployglot
<img width="861" alt="ployglot" src="https://user-images.githubusercontent.com/22510081/96370732-d66bf580-1199-11eb-8a76-39d6d93de579.png">

12. Self-healing (liveness probe)
<img width="907" alt="Liveness1" src="https://user-images.githubusercontent.com/22510081/96409630-8dab4f80-1220-11eb-8e6e-2fed6a32fade.png">
<img width="1027" alt="Liveness2" src="https://user-images.githubusercontent.com/22510081/96409628-8c7a2280-1220-11eb-8475-a9eb70b45fd9.png">

