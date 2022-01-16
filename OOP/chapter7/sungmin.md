# 7장 모듈을 통한 역할 공유

## 7.1 역할 이해하기
- 연관이 없는 객체가 서로 비슷한 역할을 수행하기 시작할 때 이 객체들은 주어진 역할을 누군가를 위해서 관계를 맺는다.
- 역할을 사용하면 역할과 관련된 객체들 사이에 의존성이 생겨난다.

### 7.1.1 역할 찾기
- 메시지 시그니처만 공유하는 것이 아니라 특정 행동까지 공유해야 하는 역할이 있다.
- 역할 수행자들이 행동을 공유해야 할 경우에는 공통의 코드를 어떻게 정리할지 고민해야 한다.
- 객체지향 언어들은 메서드의 묶음에 이름을 부여하고 관리할 수 있는 방법을 제공한다. 메서드는 모듈 속에서 정의되고 어느 객체든 이 모듈을 추가할 수 있다.
- 코드를 모듈에 작성하고 객체가 모듈을 추가하면 이 객체가 반응할 수 있는 메시지의 수를 확장한 것이 된다.
- 객체는 다음에서 설명하는 내용에 부합하는 모든 메시지에 반응할 수 있다
    - 스스로가 구현하고 있는 메시지
    - 상위에 있는 모든 객체가 구현한 메시지
    - include한 모든 모듈이 구현하고 있는 메시지
    - 상위의 있는 모든 객체가 include하고 있는 모든 모듈이 구현하고 있는 메시지

### 7.1.2 책임 관리하기
- 모듈은 클래스에 세부사항에 대해 모르는 채로 메시지를 전송해야 한다.

### 7.1.3 불필요한 의존성 제거하기
- 특정 클래스 이름을 명시하지 않고 메시지를 "target"에게 보내는 것으로 표현한다.
- target의 클래스에 전혀 관심이 없고 그저 target이 특정한 메시지에 반응할 수 있기를 바랄 뿐이다.
- 이러한 타입은 5장의 오리 타입과 매우 비슷하게 생겼다

#### 객체가 자기 스스로를 표현할 수 있게 하기
- 객체는 자기 스스로를 관리할 수 있어야 한다

### 7.1.4 구체적인 코드 작성하기
- 우리는 두 가지를 결정해야 한다. 코드가 무엇을 해야 하는지, 그리고 코드를 어디에 두어야 하는지
- 객체는 스스로를 표현할 수 있어야하고, 제 3자의 도움 없이도 소통할 수 있어야 한다.

### 7.1.5 추상화하기
- 모듈이 메시지를 전송한다면 그 구현 역시 직접 가지고 있어야 한다.
- 이러한 메서드는 템플릿 메서드 패턴을 따르는 훅(hook) 메서드이다.
- 상속인 것과 상속처럼 행동하는 것의 차이는 매우 중요하며, 어느 것을 선택하는가에 따라 그 효과도 다르다.
- 두 코딩 기술을 매우 비슷한데, 둘 모두 자동화된 메시지 전달(automatic message delegation)에 기반하고 있다

### 7.1.6 메서드를 찾아 올라가기
- 메시지가 인스턴스에 전송되었을 때 메서드를 찾지 못했다면 탐색은 상위클래스로 이어진다.

### 7.1.7 역할의 행동 상속받기
- 다른 모듈을 include하는 모듈을 만들어 다른 모듈이 정의하고 있는 메서드를 재정의하는 모듈도 만들 수 있다.
- 길게 늘어선 상속관계를 만들어 놓고 관계 중간 여러 층위의 클래스들에 모듈들을 include 할 수 있다
- 이 기술은 강력하지만 매우 위험하다. 하지만 이것을 이용해 객체들 사이의 간단한 구조를 만들 수 있게 된다.

## 7.2 상속받을 수 있는 코드 작성하기

### 7.2.1 안티패턴 알아채기
- 상속을 적용하면 좋을 것 같다고 말해주는 두 개의 안티패턴이 있다
    - type이나 category와 같은 이름을 가진 변수가 있고 이 변수를 가지고 self에 어떤 메시지를 전송할지 결정하는 경우
    - 객체의 클래스를 확인 하고 어떤 메시지를 전송할지 판단하고 있다면 오리타입을 놓치고 있다는 뜻이다.

### 7.2.2 추상화된 코드를 모두 사용하기
- 모든 하위클래스가 아닌 몇몇 하위클래스에게만 적용되는 코드는 상위클래스에 포함되면 안된다.
- 이 원칙은 모듈에도 적용될 수 있다.
- 공통으로 사용할 만한 추상화된 코드가 없다면 주어진 디자인 이슈의 해결책은 상속이 아니다.

### 7.2.3 약속을 존중하라
- 하위클래스는 자신의 인터페이스를 충실히 따르는 것이 기대대로 행동하는 것이다.
- 다른 객체가 타입을 확인해서 클래스를 어떻게 다루어야 하는지 판단하거나 어떤 출력을 기대할 수 있는지 판단하게 만들어서는 안된다.

### 리스코프 치환원칙
- 약속을 존중한다는 것은 리스코프 치완 원칙을 따른다는 것과 같다.
- SOLID 디자인 원칙의 'L'도 이 리스코프 치환 원칙을 뜻한다.
- 상위타입(supertype)은 자신의 하위타입(subtype)으로 치환될 수 있어야 한다.

### 7.2.4 템플릿 메서드 패턴 사용하기
- 상속받을 수 있는 코드를 작성하기 위한 가장 핵심적인 기술은 템플릿 메서드 패턴이다.
- 템플릿 메서드 패턴 덕분에 추상적인 것과 구체적인 것을 구분할 수 있다.

### 7.2.5 한발 앞서 클래스 사이의 결합 깨뜨리기
- 상속받은 클래스가 super를 전송해야 하는 코드를 작성하지 말자. super를 전송해야 하는 코드는 또 다른 의존성을 추가하는 것이다
- 훅 메서드를 이용해 하위클래스가 개입할 수 있는 여지를 제공하면서 동시에 하위클래스에게 추상적인 알고리즘에 대해 알아야 하는 책임을 지우지 않을 수 있다.

### 7.2.6 상속 관계(상속구조)를 낮게 만들기
- 훅 메서드의 한계는 상속 관계의 높이를 낮게 만들어야 하는 많은 이유 중 하나일 뿐이다.
- 높고 넓은 상속 관계는 이해하기 어렵고, 유지보수 비용도 비싸다. 그리고 가능한 피하는 것이 좋다.
- 객체는 자신보다 위에 있는 모든 것에 의존하고 있기 때문에 높은 상속 관계는 이미 수많ㅇ느 붙박이 의존성을 가지고 있는 셈이다.
- 중간 클래스들을 수정하면 에러가 만들어지 가능성이 매우 높아진다.

## 7.3 요약
- 같은 역할을 수행하는 객체가 행동을 공유해야 할때 묘듈을 사용한다.
- 모듈에 정의된 코드는 어떤 객체에든 추가할 수 있다.
- 모듈과 상속은 매우 비슷하다. 때문에 모듈을 include한 객체가 행동을 추가할 때 템플릿 메서드 패턴을 사용하자. 그리고 훅 메서드를 사용해서 super를 전송하지 않도록 하자
- 리스코프 치환 원칙: "상위타입은 자신의 하위타입으로 치환될 수 있다"