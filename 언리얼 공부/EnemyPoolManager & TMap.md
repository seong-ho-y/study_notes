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

SpawnActorDeferred