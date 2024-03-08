---
title: Unreal Engine5 LineTrace와 Collision
date: 2024-03-08 23:50:00 +09:00
categories: [ Unreal Engine ]
tags: [ Unreal Engine, Game, Programming ]
---

> 이번 포스팅은 [프로젝트 기반으로 배우는 언리얼 엔진 5 게임 개발 2/e]
> 도서를 참고하였다.

# LineTrace, Collision

유니티에서 RayCast가 있다면 언리얼에는 LineTrace가 있다. 이 LineTrace를
이용하여 직선 상에 충돌한 물체와 상호작용을 할 수가 있다. 그 상호작용을
Collision으로 처리한다.

# 1. Collision

충돌은 세 가지 유형이 있다.

- Ignore: 충돌 했을 때 아무런 상호작용없음.
- Overlap: 겹칠 수 있음.
- Block: 서로 겹치는 게 불가능.

그래서 충돌은 두 가지 유형으로 나눌 수 있다.

- 두 물체가 상호작용 하되 물리적 충돌은 없음.
- 두 물체가 물리적 충돌을 함(물리적 충돌 자체를 상호작용으로 볾)

Ignore 제외하고는 서로 상호작용을 하는 것이다.
각 충돌 유형별로 어떻게 되는 지 눈으로 확인해보자.

## Ignore

![Game Panel](assets/UE5TPS/Collision with LineTrace/cwl-0-1.png)

![Detail Panel](assets/UE5TPS/Collision with LineTrace/cwl-0-2.png)
> Trace Responses에서 Camera를 Ingnore한 것은 예시를 보여주려면
> 시야를 확보해야 해서 누른 것이니 신경 쓸 필요 없다.
> 라인 트레이스를 이해하면 왜 이렇게 한 것인지 알 수 있다.

Collision 섹션에서 프리셋을 Custom으로 하여 확인한다. 캐릭터 클래스는
Pawn을 상속한 것이니 Pawn을 Ignore해주면 된다.

## Overlap

오버랩은 보이는 것 자체는 Ignore랑 별 차이가 없다. Overlap을 확인하기 위해
메서드로 로그를 출력해볼 것이다.

헤더파일

```cpp
UFUNCTION()
void OnBeginOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp,
		int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult);
	
UFUNCTION()
void OnEndOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor, UPrimitiveComponent* OtherComp,
		int32 OtherBodyIndex);
```

cpp파일

```cpp
void ATpsCharacter::OnBeginOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor,
	UPrimitiveComponent* OtherComp, int32 OtherBodyIndex, bool bFromSweep, const FHitResult& SweepResult)
{
	UKismetSystemLibrary::PrintString(this, TEXT("Begin Overlap"), true, false, FLinearColor::Red, 1.f);
}

void ATpsCharacter::OnEndOverlap(UPrimitiveComponent* OverlappedComponent, AActor* OtherActor,
	UPrimitiveComponent* OtherComp, int32 OtherBodyIndex)
{
	UKismetSystemLibrary::PrintString(this, TEXT("End Overlap"), true, false, FLinearColor::Red, 1.f);
}
```

그 다음 캐릭터의 Collision Preset을 수정한다.

![Collision Preset](assets/UE5TPS/Collision with LineTrace/cwl-0-3.png)

그 다음 액터를 월드에 배치하고 테스트 해본다.

![Test](assets/UE5TPS/Collision with LineTrace/cwl-0-4.png)  
로그가 두 번 뜨는 이유는 캐릭터와 배치한 액터 모두 같은 클래스를 상속했기
때문이다.

BeginOverlap은 겹치기 시작할 때 호출되는 메서드다.
EndOverlap도 있지만 Begin만 테스트 해본다.

Unity를 사용해본 사람이라면 이것으로 트리거라는 것을 만들 수 있다는 것을 알 수 있다.

Block은 따로 설명할 필요 없이 서로 물리적 충돌을 하는 모습이 Block으로
설정된 것이다.

# 2. LineTrace

라인 트레이스는 일직선 상으로 레이저를 날린다고 생각하면 이해가 쉽다.
그리고 그 레이저에 닿은 물체(또는 물체들)의 정보를 받아서 충돌 처리를
할지 말지 결정할 수 있다.

라인 트레이스는 다음과 같이 사용할 수 있다.

```cpp
GetWorld()->LineTraceSingleByChannel(Hit, StartTrace, EndTrace, Channel, CollisionParams);
```

| Variable            | Type                    | Description        |
|---------------------|-------------------------|--------------------|
| **Hit**             | *FHitResult*            | 충돌 정보              |
| **StartTrace**      | *FVector*               | 레이저의 시작지점          |
| **EndTrace**        | *FVector*               | 레이저가 끝나는 지점        |
| **Channel**         | *ECollisionChannel*     | 라인 트레이스 채널         |
| **CollisionParams** | *FCollisionQueryParams* | 무시할 액터나 컴포넌트 정보 전달 |

> 여기서 트레이스 채널은 라인 트레이스의 고유한 특성이라고 생각하면 된다.

## 2-1. 커스텀 트레이스 채널 만들기

프로젝트 세팅으로 들어가서 Trace를 검색하여 커스텀 트레이스 채널을 만든다.

![Create Trace Channel](assets/UE5TPS/Collision with LineTrace/cwl-0-5.png)

만들었으면 월드에서 스태틱 메시 하나를 복사해서 디테일에서 Collision섹션으로 이동한다.

![Trace Channel](assets/UE5TPS/Collision with LineTrace/cwl-0-6.png)

기본적인 트레이스 채널(Visibility, Camera)과 직접 만든 채널 1개가 있다.

## 2-2. 라인 트레이스 실행하기

라인트레이스를 실행한다. 다음과 같은 코드를 추가하였다.

```cpp
FHitResult Hit;
FVector StartTrace = Mesh->GetSocketLocation("Muzzle");
FVector ForwardVector = Mesh->GetSocketRotation("Muzzle").Vector();
FVector EndTrace = ((ForwardVector * 10000.f) + StartTrace);
FCollisionQueryParams CollisionParams;
CollisionParams.AddIgnoredActor(this);
CollisionParams.AddIgnoredActor(GetOwner());
GetWorld()->LineTraceSingleByChannel(Hit, StartTrace, EndTrace, ECC_GameTraceChannel1, CollisionParams);
DrawDebugLine(GetWorld(), StartTrace, EndTrace, FColor::Red, false, 1, 0, 1);
UKismetSystemLibrary::PrintString(this, Hit.bBlockingHit ? TEXT("Hit"):TEXT("None"), true, false, FLinearColor::Red, 1.f);
```

해당 코드는 메서드 내에 작성 되었다. 하나 하나 뭔지 이해 해보자.

```cpp
FHitResult Hit;
FVector StartTrace = Mesh->GetSocketLocation("Muzzle");
FVector ForwardVector = Mesh->GetSocketRotation("Muzzle").Vector();
FVector EndTrace = ((ForwardVector * 10000.f) + StartTrace);
```

* Hit은 충돌정보를 담을 변수이다.
* 어느 방향으로 발사할지를 FowardVector로 결정하고 EndTrace를 계산한다.
* EndTrace(종점) = (방향 * 사거리) + StartTrace(시점)

```cpp
FCollisionQueryParams CollisionParams;
CollisionParams.AddIgnoredActor(this);
CollisionParams.AddIgnoredActor(GetOwner());
```

* 무시할 액터의 정보를 담을 변수 추가
* 무시할 액터들을 추가한다.

```cpp
GetWorld()->LineTraceSingleByChannel(Hit, StartTrace, EndTrace, ECollisionChannel::ECC_Visibility, CollisionParams);
DrawDebugLine(GetWorld(), StartTrace, EndTrace, FColor::Red, false, 1, 0, 1);
UKismetSystemLibrary::PrintString(this, Hit.bBlockingHit ? TEXT("Hit"):TEXT("None"), true, false, FLinearColor::Red, 1.f);
```

* 라인트레이스를 실행한다.
* 라인트레이스를 디버깅을 통해 시각화한다.
* 상호작용 여부를 출력한다. 상호작용 시 Hit, 아니면 None

> ECollisionChannel::ECC_Visibility은 언리얼에서 기본적으로 제공하는 채널이다.
커스텀으로 만든 채널의 경우에는 ECollisionChannel::ECC_GameTraceChannel1이고 
커스텀 채널은 만들 때 마다 차례로 뒤에 숫자가 커진다.

컴파일하고 상호작용을 확인해본다.  

![Test](assets/UE5TPS/Collision with LineTrace/cwl-0-7.png)  
물체가 해당 트레이스 채널을 Block하는 상태에서 Hit가 출력되므로 정상이다.

이것으로 이번 포스팅을 마친다.
