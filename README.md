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
oss-order : 8081
oss-activation : 8082
oss-scheduling : 8083
oss-fieldwork : 8084
oss-gateway : 8088 (local), 8080 (docker)

1. IntelliJ 프로젝트 구성
<img width="1280" alt="IntelliJ_Project" src="https://user-images.githubusercontent.com/22510081/96367999-10ce9600-118c-11eb-8867-d338145c812b.png">
2. GitHub Repository 구성
<img width="1280" alt="GitHub_Repository" src="https://user-images.githubusercontent.com/22510081/96368002-14621d00-118c-11eb-882e-37aa69209812.png">

# 업무처리 흐름
1. Local Scenario 1 (청약 후 청약취소)
 - 청약
<img width="1214" alt="flow1-1" src="https://user-images.githubusercontent.com/22510081/96368169-68b9cc80-118d-11eb-9f89-0779028cf5eb.png">
<img width="1214" alt="flow1-1_kafka" src="https://user-images.githubusercontent.com/22510081/96368170-68b9cc80-118d-11eb-8c13-614ec15a0d37.png">
<img width="1214" alt="flow1-2" src="https://user-images.githubusercontent.com/22510081/96368171-69526300-118d-11eb-9aa5-26f3468ccb89.png">
<img width="1214" alt="flow1-2_kafka" src="https://user-images.githubusercontent.com/22510081/96368173-69526300-118d-11eb-9087-02c46e60fdb4.png">
<img width="1214" alt="flow1-2_myPage" src="https://user-images.githubusercontent.com/22510081/96368174-69eaf980-118d-11eb-9c09-83c82d013737.png">
<img width="1214" alt="flow1-3" src="https://user-images.githubusercontent.com/22510081/96368166-66f00900-118d-11eb-84b3-58845cff0cf7.png">
<img width="1214" alt="flow1-3_kafka" src="https://user-images.githubusercontent.com/22510081/96368167-67889f80-118d-11eb-8de6-27e7c9d59ddc.png">
<img width="1214" alt="flow1-3_myPage" src="https://user-images.githubusercontent.com/22510081/96368168-68213600-118d-11eb-8369-efefc6cd21f7.png">



# 평가항목
1. Saga

2. CQRS

3. Correlation

4. Request/Response

5. Gateway

6. Deploy/Pipeline

7. Circuit Breaker

8. Autoscale(HPA)

9. Zero-downtime deploy (readiness probe)

10. ConfigMap/Persisteence Volume

11. Ployglot

12. Self-healing (liveness probe)
