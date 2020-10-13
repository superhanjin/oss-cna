# oss-cna

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
 1. 비기능적 요구사항
  - 트랜잭션
    스케줄링 호출이 되지 않으면, Activation 상태는 그대로 유지가 된다. Sync 호출
  - 장애격리
    Activation 기능이 수행되지 않더라도 청약 접수는 365일 24시간 진행 될 수 있어야 Async (event-driven), Eventual Consistency
    스케줄링 서비스의 부하가 과중되면 호출을 잠시동안 받지 않고 잠시후에 하도록 유도한다 Circuit breaker, fallback
  - 성능
    청약자는 개통처리 상황을 마이페이지(프론트엔드)에서 확인할 수 있어야 한다 CQRS

# 모델링
![이벤트스토밍](https://user-images.githubusercontent.com/22510081/93422213-40208780-f8ee-11ea-88b5-5b437e0f386c.png)
