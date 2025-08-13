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

## 가비지 컬렉터

"분명히 `BeginPlay`에서 적을 생성해서 배열에 넣었는데 왜 `nullptr`이 되는가?"

그 이유는 바로 언리얼 엔진의 **가비지 컬렉터(Garbage Collector, GC)** 때문입니다.

1. `BeginPlay`에서 10마리의 적 액터가 성공적으로 스폰됩니다.
    
2. 이 액터들의 포인터(주소값)가 풀의 `TArray`에 저장됩니다.
    
3. 잠시 후, 언리얼 엔진의 가비지 컬렉터가 메모리를 정리하기 위해 순찰을 돕니다.
    
4. GC는 "이 10마리의 적 액터들을 누가 사용하고 있나?"라고 확인합니다.
    
5. 만약 풀의 `TArray`에 **`UPROPERTY()` 매크로가 없다면**, GC는 이 배열을 인식하지 못합니다.
    
6. GC는 "아무도 이 적들을 사용하지 않네. 메모리를 차지하니 청소해야겠다!"라고 판단하여 **10마리의 적 액터를 모두 파괴**해버립니다.
    
7. 그 결과, 풀의 배열에는 파괴된 액터를 가리키던 10개의 텅 빈 포인터(`nullptr`)만 남게 됩니다.
    

`UPROPERTY()`는 "가비지 컬렉터님, 이 변수가 가리키는 오브젝트는 중요한 것이니 지우지 마세요!"라고 알려주는 **꼬리표** 역할을 합니다. 이 꼬리표가 없어서 문제가 발생한 것입니다.
# 해결방법
UPROPERTY로 언리얼 엔진에게 알리기