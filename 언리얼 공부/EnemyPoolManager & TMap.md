PoolManager를 사용하는 이유
- 다량의 적들을 소환하고 관리할 때에는 매번 객체를 새로 생성하는게 아닌 미리 생성해두고 setActive(false)를 해둠
- 객체만 생성해두는거임
- 그 이후 Spawn을 통해 생성해둔 객체를 맵에 소환
- 최적화 + 적 관리에 유용

## TMap<> 맵
언리얼에서 TArray 다음으로 많이 쓰는 타입


# !!!풀링한 객체들을 불러올 때!!!

위치지정까지 꼭 해주기

```C++
AEnemyBase* Enemy = PoolManager->GetPooledEnemy(EEnemyType::Walker);
if (Enemy)
{
	FVector SpawnLocation = GetRandomSpawnLocation();
	Enemy->SetActorLocation(SpawnLocation);
	Enemy->SetActive(true);
}
```

Enum을 사용하면 편리하게 관리할 수 있다
근데 현재 nullptr참조 오류가 계속나서 답이 없음 5.15 기준
일단 Enum 안쓰고 개발해보자

TMap<TSubclassOf<AEnemyBase>, TArray<AEnemyBase*>>
편의상 TMap을 쓴다
적의 클래스 구조가 EnemyBase를 상속받아서 그 하위 클래스별로 적이 나누어져있기 때문

여기서 클래스를 인자로 받는게 아니라 인스턴스(객체)를 사용
이거때문에 인스턴스를 미리 생성을 해놓고 객체로 주어야함
이거 안해서 여태까지 nullptr오류 난듯

SpawnActor를 미리 해놓기 setActive(false)
이게 풀링을 해놓은 상태
GetPool을 하게되면 위치지정하고 setActive(true)로 해서 연산 시작

SpawnActorDeferred