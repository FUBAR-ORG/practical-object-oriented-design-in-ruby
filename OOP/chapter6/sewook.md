# 상속을 이용해 새로운 행동 얻기

객체지향 언어들은 언어의 문법 자체에 상속이라는 기능을 통해 코드를 공유할 수 있다.

<br>

## 고전적 상속 이해하기

- 기본적으로 상속이란 자동화된 메시지 전달(automatic message delegation) 시스템이다.
- 특정 객체가 이해할 수 없는 메시지를 전달받았을 경우 그 객체는 메시지를 다른 객체에게 전달한다. 이러한 전달 관계를 만드는 것이 상속이다.
- 고전적인 상속 관계는 하위 클래스를 만드는 것을 통해 정의되며 메시지는 하위클래스에서 상위클래스로 전달된다.

<br>

## 상속을 사용해야 하는 지점을 알기

### 구체 클래스에서 시작하기

- 자전거 클래스(Bycicle)에는 전반적인 크기, 체인, 타이어 크기가 이미 구현되어 있다.
- 로드 자전거에는 핸들바 테이프가 필요하고, 마운틴 자전거에는 서스펜션이 필요하다는 점만 다르다.

### 자전거 종류 추가하기

- 구체 클래스에 이미 필요한 행동들이 구현되어 있기 때문에 이 클래스에 코드를 추가해서 문제를 해결할수도 있다.
- 예를 들어, Bycicle 클래스에 if문을 작성해 자전거의 style에 따라 다른 행동을 하게 작성할 수 있다.
- 이러한 패턴은 겉보기엔 괜찮지만 문제가 많은 안티패턴(antipattern)이다.
- 객체의 클래스를 자기가 어떤 종류인지 알고 있는 어트리뷰트는 "나는 네가 누구인지 알고있다. 때문에 네가 무엇을 하는지도 안다." 라고 말한다. 이 지식은 수정비용을 높이는 의존성이다

### 숨겨진 타입 찾아내기

- 하나의 클래스가 서로 다르지만 연관된 여러 타입을 가지고 있다면 상속을 통해 이를 해결할 수 있다.
- 밀접히 연관된 타입들이 행동을 공유하고 있지만 특정한 관점에서는 다른 겨우에 상속을 적용할 수 있다.

### 상속을 선택하기

- 객체는 메시지를 수신하며 메시지를 직접 처리하거나 다른 객체가 처리할 수 있도록 메시지를 넘기는 방식으로 메시지를 처리한다.
- 첫 번째 객체가 이해할 수 없는 메시지를 수신하면 다음 객체에게 자동으로 메시지를 전달한다. 상속은 이러한 두 객체의 사이를 정의한다.
- 여러 개의 부모를 가지고 있는 객체가 이해할 수 없는 메시지를 수신했을 때 다음과 같은 문제가 발생한다.
  - 어느 부모에게 메시지를 넘겨야할까?
  - 둘 이상의 부모가 이 메시지에 대한 처리를 구현하고 있다면 어느 부모에게 우선권이 있는가?
- 이러한 문제를 해결하기 위해 하위클래스는 하나의 상위클래스만 가질 수 있는 단일상속을 지원한다.

<br>

## 상속의 잘못된 사용

- 구체 클래스에 일반적인 행동(Bycicle)뿐만 아니라 특수한 행동(RoadBike)이 섞여있으면 상속받는 객체(MountainBike)는 원하지도 않고 필요로도 하지 않는 행동을 상속받는다.

<br>

## 추상화 찾아내기

- 하위클래스는 상위클래스의 특수한 형태이다.
- 하위클래스(MountainBike)는 상위클래스(Bycicle)의 모든 행동을 갖고 있고 추가적인 행동을 더 가지고 있어야한다.
- 상위클래스(Bycicle)과 협업할 수 있는 모든 객체는 하위클래스(MountainBike)에 대해 아무것도 모른채 하위클래스와 협업할 수 있어야 한다.
- 상속이 제대로 작동하려면 모델링하는 객체들이 명백하게 일반-특수 객체를 따르며 올바른 코딩 기술을 사용해야 한다.

### 추상화된 상위클래스 만들기

- 추상 클래스는 상속받기 위해 존재한다.
- 추상 클래스는 하위클래스들이 공유하는 공통된 행동들의 저장소이다.
- 추상 클래스를 상속받은 하위클래스들은 구체적인 형태를 제공할 수 있다.
- 상속 비용을 최소화 하는 가장 좋은 방법은 하위클래스가 추상 클래스를 필요하기 직전에 추상 클래스를 만드는 것이다.
- 중복 코드를 계속해서 변경해야 한다면 상속 관계를 만드는 것이 나은 선택이다.
- 하위클래스의 코드를 상위클래스로 올리는 것이 상위클래스의 코드를 하위클래스로 내리는 것보다 수월하기 때문이다.

### 추상적인 행동을 위로 올리기

- 구체적인 구현을 아래로 내리는 방식으로 구체 클래스에서 추상 클래스로 변경하면 구체적인 행동을 상위클래스에 남겨놓는 실수를 할 수 있다.
- 구체적인 행동은 하위클래스에 적용될 수 없는 행동이므로 이러한 상속 관계는 믿을 수 있는 것이 아니다.
- 구체적인 것을 내리기보다는 추상적인 것을 끌어올리는 방식을 선택해야 한다.

### 구체적인 것들 속에서 추상적인 것 분리해내기

- 행동의 특정 부분을 공유하려고 하면 구체적인 것과 추상적인 것을 분리해야 한다.
- 모든 하위 객체가 공유해야 하는 부분을 위로 올려야 한다.

### 템플릿 메서드 패턴 사용하기

- 템플릿 메서드 패턴이란 기본 구조를 상위클래스가 정의하고 상위클래스에서 메시지를 전송하여 하위클래스의 특수한 값을 얻는 기술이다.

### 모든 템플릿 메서드 구현하기

- 템플릿 메서드 패턴을 사용하는 클래스는 자신이 전송하는 메서드를 직접 구현해 놓아야 한다.
- 템플릿 메서드 패턴을 사용할 때는 언제나 호출되는 메서드를 작성하고 유용한 에러 메시지를 제공해야 한다.

<br>

## 상위클래스와 하위클래스 사이의 커플링 관리하기

### 커플링 이해하기

- 다른 클래스에 대해 알고 있다면 여기서 의존성이 만들어진다.
- 이 의존성은 객체를 강하게 결합시키며 하위클래스가 super를 전송하면서 만들어진다.
- 하위클래스는 자신이 무엇을 해야 하는지 알야아 하며 상위클래스와 어떻게 소통해야 하는지도 알고 있어야 한다.
- 하위클래스가 super를 전송한다는 것은 스스로 알고리즘에 대해 알고 있다고 말하는 것이며 이 지식에 의존하고 있는 것이다.

### 훅 메시지를 사용해서 하위클래스의 결합 없애기

- 훅 메시지는 정해진 메서드 구현을 통해 하위클래스가 정보를 제공할 수 있도록 만들어주는 메시지이다.
- 훅 메시지를 사용하면 하위클래스는 알고리즘에 대해 몰라도 되며, 상위클래스가 모든 권한을 가질 수 있게 된다.
- 메시지를 언제 전송할지를 상위클래스가 관리한다는 것은 하위클래스를 변경하지 않고도 알고리즘을 수정할 수 있다는 뜻이다.
