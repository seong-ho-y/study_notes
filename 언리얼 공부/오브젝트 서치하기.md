TActorIterator<AEnemyBase> It(GetWorld());
- `GetWorld()`에 존재하는 모든 `AEnemyBase` 타입의 액터들을 순회하는 반복자입니다.
    
- C++의 일반적인 `for (int i = 0; i < N; ++i)` 대신, **게임 월드에 존재하는 특정 클래스의 액터들을 순회**할 수 있게 해주는 언리얼 전용 반복 도구입니다.


for (TActorIterator<AEnemyBase> It(GetWorld()); It; ++It)
{
    AEnemyBase* Enemy = *It; // 반복자에서 포인터 꺼내기
    if (Enemy)
    {
        Enemy->TakeDamage(10);
    }
}

|코드|의미|
|---|---|
|`TActorIterator<AEnemyBase>`|AEnemyBase 클래스를 대상으로 순회|
|`It(GetWorld())`|현재 월드에서 해당 타입을 찾음|
|`*It`|현재 순회 중인 액터를 포인터로 반환|
|`It; ++It`|다음 액터로 이동할 수 있을 때까지 순회|

## ✅ 언제 쓰는가?

- 게임 월드에 존재하는 모든 적 찾기
    
- 특정 컴포넌트를 가진 액터들 검사
    
- 디버깅이나 조건 체크 용도
    

---

## ✅ 참고: 이걸 쓰기 위해 필요한 헤더

`#include "EngineUtils.h"`

없으면 `TActorIterator`가 인식되지 않음