Manager 클래스를 만들어서 서로 참조하면서 쓰면 불편하다
사실 아직까지 불편한지는 모르겠는데 제미나이 피셜 비추

Event를 써보자

Event를 담당하는 클래스 하나를 만들어준다
방송국이라고 비유를 하겠다
적이 죽었다는 이벤트를 발생시키면 방송국을 거쳐 수신하는 사람이 생긴다
이 과정에서 이벤트 발생자와 수신자는 서로를 몰라도 된다
이러한 특징이 Event의 장점이다


헤더에는 using System이 필요함
static class로 선언을 하여 모든 곳에서 방송국에 발송, 수신 받을 수 있게 해준다
event를 생성해준다
public static event Action\<Enemy> OnEnemyDied;
방송 채널 (주파수)를 만들었다고 생각하면 된다
public static void ReportEnemyDied(Enemy enemy){
	OnEnemyDied;
}
도 만들어준다
여기서 방송을 Invoke로 호출한다

OnEnemyDied += 을 통해서 방송을 구독해놓을 수 있다
이렇게 되면 방송이 호출 되어 수신될 시 자동으로 구독해놓은 메서드가 실행된다
그렇기에 사용하지 않을 때에는 -=으로 해지해놓아야한다 (메모리 누수 방지)