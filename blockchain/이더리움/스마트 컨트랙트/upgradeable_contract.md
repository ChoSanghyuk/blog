# 업그레이더블 컨트랙트





업그레이더블 컨트랙트(upgradeable contract)란 저장된 데이터는 그대로 유지된 채, 기능 로직이 수정 가능한 컨트랙트를 의미한다. 

일반적으로 스마트 컨트랙트는 한 번 배포된 이상 수정이 불가능한 것으로 알려져있다. 실제로도 이더리움에서는 모든 트랜잭션은 불변(immutable)하기에, 배포된 컨트랙트 자체는 수정할 수 없다. 때문에 전통적인 패턴으로는 배포된 컨트랙트에 대해서는 기능의 추가, 버그 수정이 불가능했다. 컨트랙트의 수정이 불가피할 경우에는 새로운 컨트랙트를 배포하고, 이전 컨트랙트의 정보를 다시 저장시킨 후, 새로운 컨트랙트를 사용하는 수 밖에 없었다.

이러한 한계를 극복하기 위해 프록시 패턴을 사용하여 컨트랙트를 업그레이드 가능하도록 배포하는 방안이 제안되었다. 



---



#1 기본 원리

우선, 프록시란 클라이언트가 사용하려는 실제 대상인 것처럼 위장해서 요청을 받는 대리인을 의미한다. 프록시는 요청의 일부 부가기능만을 수행하고, 최종적으로 타겟에게 요청을 위임하는 구조를 가진다. 

업그레이더블 컨트랙트란 데이터 저장을 담당하는 프록시 컨트랙트를 두고, 실제 기능 수행은 별도의 타겟 컨트랙트에게 위임하는 컨트랙트를 의미한다. 프록시 컨트랙트는 타겟 컨트랙의 주소를 저장해두고, 사용자의 요청이 올 때마다 해당 주소에게 요청을 위임시킨다. 만약, 로직의 변경이 필요한 경우에는 로직 컨트랙트만을 새로 배포한 후에, 프록시 컨트랙트가 가지고 있는 타겟의 주소를 새 컨트랙트의 주소로 변경해준다. 클라이언트 입장에서는 자신이 바라보고 있는 컨트랙트의 주소와 내부 정보의 변동 없이, 새로운 로직을 사용할 수 있다.

<프록시 구조 설명 그림>

![image-20240912133329538](C:\Users\Owner\AppData\Roaming\Typora\typora-user-images\image-20240912133329538.png)



이때, '데이터 저장은 프록시 컨트랙트에 하고, 실제 기능 수행은 타겟 컨트랙트에게 위임' 할 수 있게 해주는 것이 `delegatecall`이다.

`delegatecall`은 이더리움의 opcode 중 하나로, **다른 컨트랙트의 코드를 사용하되 실행 환경(Context)은 기존 컨트랙트에서 수행**될 수 있도록 한다. 예를 들어, A 컨트랙트가 B 컨트랙트를 호출할 때, `delegatecall`을 이용하게 되면 B 컨트랙트의 Code를 사용하지만, Storage는 A 컨트랙트를 사용하게 된다. 트랜잭션 실행의 컨텍스트(Context)가 그대로 유지되는것이 `delegatecall`의 핵심이다.

<delegate call 동작 설명 그림>

![image-20240913104550578](C:\Users\Owner\AppData\Roaming\Typora\typora-user-images\image-20240913104550578.png)

:bulb: account 기본 구조

- nonce :  EOA에서 보내진 트랜잭션 갯수를 세는 지표
- balance : 이 주소가 가지는 있는 이더(웨이) 수량
- code hash : 계정이 가지는 EVM에서의 실행 코드를 나타내는 해시값
- storageRoot(storage hash) : 계정 저장 공간을 인코딩하는 머클 트리의 루트 노드 해시값



이처럼 deletecall를 이용한 프록시 패턴이 업그레이더블 컨트랙트를 구현하는 기본 개념이다.



---



#2 프록시 패턴에서 발생하는 문제들



프록시 패턴을 사용할 때, 일반적인 컨트랙트 개발과 동일하게 개발한다면 몇가지 문제들이 발생한다.



초기화 문제

프록시 패턴에서는 로직 컨트랙트의 초기 상태를 설정할 때, `constructor()`을 사용할 수 없다.

`constructor()`는 컨트랙트가 배포될 때 단 한 번만 실행되며, 이미 배포된 컨트랙트는 constructor 관련 정보를 가지고 있지 않는다. 프록시 컨트랙트는 이미 배포된 상태의 로직 컨트랙트를 바라보기 때문에, 프록시 컨트랙트에서 로직 컨트랙트의 `construct()`를 실행시킬 방법은 없다. 

또한, 데이터 저장은 프록시 컨트랙트에 해야하는 로직 컨트랙트 입장에서, 프록시 컨트랙트에 연결되기 이전에 초기 상태를 설정하는 것은 아무런 의미도 없다.



충돌문제

프록시 패턴에서는 3가지의 충돌 문제가 발생할 수 있다.



케이스1. 프록시의 변수와 로직 컨트랙트의 변수간 슬롯 충돌

솔리디티는 컨트랙트의 변수를 선언된 순서에 맞춰서 스토리지 공간에 저장한다. 예를 들어, 첫번째 변수는 스토리지의 첫번째 slot에 배정되며, 두번째 변수는 두번째 slot에 배정되는 식이다. (단, 동적 변수는 예외적으로 해시된 위치의 스토리지 슬롯에 배정)

따라서, 기능 컨트랙트가 내부 변수를 가지고 있는 경우, 프록시의 기능 컨트랙트 주소를 저장하는 변수와 충돌이 발생하게 된다.

![image-20240913134331731](C:\Users\Owner\AppData\Roaming\Typora\typora-user-images\image-20240913134331731.png)

// 이를 해결하기 위해, EIP-1967(Standard Proxy Storage Slots)를 따라서 구현한다. (#3 구현 참고)



케이스2. 로직 컨트랙트 업그레이드 시, 슬롯 충돌

두번째로, 이전 버전의 로직 컨트랙트와 업그레이드된 새로운 버전의 로직 컨트랙트의 스토리지 충돌 가능성이 있다. 예를 들어서, 기존의 로직 컨트랙트는 변수1, 변수2, 변수3을 가지고 있었고, 새로운 버전의 로직 컨트랙에 변수 하나가 추가된다고 해보자.이때 기존의 변수들 다음에 선언되면 기존 버전과 슬롯이 충돌나지 않지만, 변수간 선언 순서가 섞이게 된다면 슬롯 충돌이 발생한다.

![image-20240913135627971](C:\Users\Owner\AppData\Roaming\Typora\typora-user-images\image-20240913135627971.png)



케이스3. 프록시 컨트랙트와 로직 컨트랙트 간의 함수 충돌 

마지막으로 프록시 컨트랙트와 로직 컨트랙트 간 함수 레벨에서 충돌이 발생하는 경우가 존재한다. 

프록시에서 타겟 컨트랙트에 로직을 위임할 때 로직별 위임 함수를 작성하는 것이 아닌, `fallback` 함수를 통해 `delegatecall`를 호출하는 방식을 사용한다. 프록시 컨트랙트에 존재하지 않는 함수 식별자를 통해 호출하게 되면, 자연스럽게 `fallback` 함수로 이어져 `delegatecall`로 로직 컨트랙트의 함수를 호출하게 되는것이다.

```solidity
// SPDX-License-Identifier: MIT
// OpenZeppelin Contracts (last updated v5.0.0) (proxy/Proxy.sol)

function _fallback() internal virtual {
    _delegate(_implementation());
}
```

그런데 만약 EVM이 인식하기에 기능 컨트랙트의 함수와 같은 함수가 프록시에 존재할 경우, fallback을 통한 위임이 아닌 프록시의 함수가 수행될 수 있다. 클라이언트는 기능 컨트랙트의 함수를 호출하였지만, 예상과 다른 결과를 받게 된다. 

특히 EVM은 함수의 식별을 할 때, 함수 시그니처를 해싱한 후 앞 4바이트만 사용한다. 같은 컨트랙트 내부에서는 솔리디티 컴파일리가 함수의 충돌을 방지하지만, 프록시와 타겟 컨트랙트는 별도의 컨트랙트임으로 함수명과 파라미터등이 다른 경우에도 얼마든지 충돌이 발생할 수 있다.

![image-20240913142024445](C:\Users\Owner\AppData\Roaming\Typora\typora-user-images\image-20240913142024445.png)









#3 구현





EIP-1967(Standard Proxy Storage Slots)

프록시의 변수와 로직 컨트랙트의 변수간 슬롯 충돌을 방지하게 위해 EIP-1967은 스토리지 슬롯을 순차적으로 사용하면 충돌 가능성이 높으니, **슬롯을 랜덤에 가깝게(pseudo-random) 배정**하는 방식을 사용한다. 프록시의 변수를 정의된 순서에 따라 순차적으로 배정하는 것이 아닌,  변수의 이름을 keccak256으로 해싱한 후 1을 뺀 값의 슬롯에 직접적으로 배정한다. keccak256 해시값을 슬롯 위치로 사용한다면, 슬롯이 충돌할 확률을 무시 가능할 정도로 낮기에 충돌 방지를 보장할 수 있게 된다. 

:memo: [참고] 1을 빼는 이유

keccak256 해시값은 유사 난수이기 때문에, account에서 keccak256을 사용해서 슬롯을 배정을 하고 있었다면 슬롯이 충돌될 가능성이 있다. 따라서 결과값에서 1을 뺌으로써 역상이 없는 난수를 만들어 충돌할 가능성을 없앤다. 

<코드>



상속너



Transparaent

UUPS

생성자초기화





#4 활용

재사용

비콘 프록시







## 개요



One of the biggest advantages of Ethereum is that every transaction of moving funds, every contract deployed, and every transaction made to a contract is *immutable* There is no way to hide or amend any transactions ever made. 



But the biggest disadvantage is that you cannot change the source code of your smart contract after it’s been deployed. Developers working on centralized applications (like Facebook, or Airbnb) are used to frequent updates in order to fix bugs or introduce new features. This is impossible to do on Ethereum with traditional patterns





## 업그레이더블 컨트랙트 패턴



### EIP-1967

### 

### Transparent 프록시 패턴

### UUPS



## 컨트랙트 재사용



## 별첨. Minimal Proxy





https://www.rareskills.io/post/erc1967
