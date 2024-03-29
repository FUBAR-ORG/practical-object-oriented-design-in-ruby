# 6장 상속을 이용해 새로운 행동얻기

## 6.1 고전적 상속 이해하기
- 상속이란 자동화된 메시지 전달 시스템이다(automatic message delegation)
- 특정 객체가 이해할 수 없는 메시지를 전달받았을 경우 그 객체는 이 메시지를 다른 객체에게 전달한다.
- 명시적으로 메시지를 위임(delegate)하는 코드를 작성하지 않아도 두 객체 사이의 상속 관계를 정의하면 자동으로 메시지 전달(하위 클래스 -> 상위클래스)이 이루어 진다

## 6.2 상속을 사용해야 하는 지점을 알기

### 6.2.1 구체 클래스에서 시작하기
- 자전거는 전반적인 크기, 체인, 타이어 크기는 이미 구현되어 있다
- 로드 자전거에는 핸들바 테이프가, 마운틴 자전거에는 서스펜션이 필요하다는 점만 다를뿐이다.
 
```ts
interface BicycleAttr {
  size?: string;
  tape_color?: string;
  chain?: string;
  tire_size?: number;
}

class Bicycle {
  public readonly size: string;
  private readonly tape_color: string;

  constructor({ size, tape_color }: BicycleAttr) {
    this.size = size;
    this.tape_color = tape_color;
  }

  get spares(): BicycleAttr {
    return {
      chain: "10-speed",
      tire_size: 23,
      tape_color: this.tape_color,
    };
  }
}

const bike: Bicycle = new Bicycle({ size: "M", tape_color: "red" });
```

### 6.2.2 자전거 종류 추가하기
- 자기가 어떤 종류인지 알고 있는 어트리뷰트를 확인하는 if문을 포함하는 안티패턴이 있다.
- 오리타입에서 언급했던 객체의 클래스를 확인하고 객체에게 어떤 메시지를 전송할지 결정하는 안티패턴이 있다.
- 객체의 클래스를 자기가 어떤 종류인지 알고 있는 어트리뷰트는 다음과 같이 말할 것이다. "나는 네가 누구인지 알고있다. 때문에 네가 무엇을 하는지도 안다." 이 지식은 수정비용을 높이는 의존성이다

```ts
type BicycleType = "road" | "mountain";

interface BicycleAttr {
  style?: BicycleType;
  size?: string;
  tape_color?: string;
  chain?: string;
  tire_size?: number;
  front_shock?: string;
  rear_shock?: string;
}

class Bicycle {
  public readonly style: BicycleType;
  public readonly size: string;
  private readonly tape_color: string;
  private readonly front_shock: string;
  private readonly rear_shock: string;

  constructor({
    style,
    size,
    tape_color,
    front_shock,
    rear_shock,
  }: BicycleAttr) {
    this.style = style;
    this.size = size;
    this.tape_color = tape_color;
    this.front_shock = front_shock;
    this.rear_shock = rear_shock;
  }

  get spares(): BicycleAttr {
    if (this.style === "road") {
      return {
        chain: "10-speed",
        tire_size: 23,
        tape_color: this.tape_color,
      };
    } else {
      return {
        chain: "10-speed",
        tire_size: 2.1,
        rear_shock: this.rear_shock,
      };
    }
  }
}

const bike: Bicycle = new Bicycle({
  style: "mountain",
  size: "S",
  front_shock: "Manitou",
  rear_shock: "Fox",
});
```
- style과 같은 변수는 숨겨진 패턴을 찾기 위한 단서가 된다.
- style은 Bicycle을 서로 다른 두 종류로 구분하고 있다.
- 하나의 클래스가 여러 개의 서로 다른, 연관된 타입을 가지고 있다.

### 6.2.3 숨겨진 타입 찾아내기
- 하나의 클래스가 여러 개의 서로 다른, 하지만 연관된 타입을 가지고 있으면 상속을 통해 해결할 수 있는 문제이다.
- 밀접히 연관된 타입들이 같은 행동을 공유하고 있지만 특정한 관점에서는 다른 경우인 것이다.

### 6.2.4 상속을 선택하기
- 객체는 메시지를 직접 처리하거나 다른 객체가 처리할 수 있도록 메시지를 넘기면서 메시지를 처리한다.
- 상속은 두 객체 사이의 관계를 정의한다. 첫 번째 객체가 이해할 수 없는 메시지를 수신하면 이 객체는 다음 객체에게 자동으로 메시지를 전달한다
- 다중상속을 지원할 경우 메시지를 전달해야할 부모를 선택해야하는 문제가 생긴다.(둘 이상의 부모가 같은 메시지에 대한 처리를 구현하고 있다면 어느 부모에게 우선권이 있는가?)
- 여러 객체지향 언어들은 이런 복잡함을 피해가기 위해 단일상속을 지원한다

## 6.3 상속의 잘못된 사용
```ts
  interface BicycleAttr {
    size?: string;
    tape_color?: string;
    chain?: string;
    tire_size?: number;
  }

  interface MountainBikeAttr extends BicycleAttr {
    front_shock?: string;
    rear_shock?: string;
  }

  class Bicycle {
    public readonly size: string;
    private readonly tape_color: string;

    constructor({ size, tape_color }: BicycleAttr) {
      this.size = size;
      this.tape_color = tape_color;
    }

    get spares(): BicycleAttr {
      return {
        chain: "10-speed",
        tire_size: 23,
        tape_color: this.tape_color,
      };
    }
  }

  class MountainBike extends Bicycle {
    private readonly front_shock: string;
    private readonly rear_shock: string;

    constructor({front_shock, rear_shock, ...args}: MountainBikeAttr) {
      super(args);
      this.front_shock = front_shock;
      this.rear_shock = rear_shock;
    }

    get spares(): MountainBikeAttr {
      return { ...super.spares, rear_shock: this.rear_shock };
    }
  }

  const mountainBike = new MountainBike({
    size: "S",
    front_shock: "Manitou",
    rear_shock: "Fox",
  });
  console.log(mountainBike.size); // "S"
  console.log(mountainBike.spares); // 잘못된 행동을 가지고 있다.
```
- constructor안에서 super를 전송하면 상위클래스의 연쇄 속으로 넘겨주게 된다. (Bicycle의 constructor메서드를 실행)
- Bicycle에 MountainBike를 억지로 끼워 넣는다면 일반적인 행동과 특수한 행동 모두를 상속받아 MountainBike에 어울리지 않는 행동들도 상속받는다.

## 6.4 추상화 찾아내기
- Bicycle 클래스의 이름은 모든 자전거가 로드 자전거였을 때는 충분히 어울리는 이름이었다.
- 하위클래스는 상위클래스의 특수한 형태이다. Bicycle과 협업할 수 있는 모든 객체는 MountainBike에 대해 아무것도 모른채 협업할 수 있어야 한다.
- 모델링하는 객체들은 명백히 **일반-특수** 관계를 따라야 한다.

### 6.4.1 추상화된 상위클래스 만들기
- 어떤 객체지향 프로그래밍 언어는 특정 클래스를 명시적으로 추상 클래스로 선언할 수 있도록 해준다 // new 메시지를 전송할 수 없다
- 추상 클래스는 상속받기 위해서 존재한다. 하위클래스들이 공유하는 공통된 행동들의 저장소이고 상속받은 하위클래스들은 구체적인 형태를 제공할 수 있다.
- 상속 관계를 만드는 데는 높은 비용이 든다. 상속 관계를 만들지 말지를 선택하는 것은 '세 번째 종류의 자전거가 얼마나 빨리 필요하게 될지', '중복 코드를 관리하는 비용이 얼마인지' 사이에 달려있다.
- 곧 세번째 종류를 구현해야 할 것 같다면 더 나은 정보를 얻을 때까지 기다리는 것이 낫다. 반면, 중복 코드를 매일 변경해야 한다면 상속 관계를 만드는 것이 보다 나은 선택일 수 있다.

### 6.4.2 추상적인 행동을 위로 올리기
- 상속을 구현하는데 따르는 여러 어려움은 구체적인 것과 추상적인 것을 제대로 구분하지 못하는 데서 기인한다.
- '위로 올리기' 전략은 실패했더라도 수정하기 쉬운 문제를 발생시킨다.
- 추상화하지 않고 빼먹은 코드가 있더라도 다른 하위클래스가 행동을 필요로 할 때가 오면 문제가 눈에 띈다.
- '위로 올리기' 전략이 아닌 '아래로 내리기' 즉, 구체적인 구현을 아래로 내리는 방식으로 변경한다면 구체적인 행동을 상위 클래스에 남겨 놓는 경우가 생길 수 있다. // 구체적인 행동은 새로운 하위클래스에 적용될 수 있는 행동이 아니다.
- 상속 관계를 진행할 때 유념해야 하는 기본 원칙은 구체적인 것을 내리기보다는 추상적인 것을 끌어올리는 방식을 취하는 것이 좋디.

### 6.4.3 구체적인 것들 속에서 추상적인 것 분리해내기
- 하위 클래스들이 같은 메서드를 각각 구현하고 있다면 추상화를 놓친 것이다.
- 구체적인 것과 추상적인 것을 분리해 행동의 특정 부분만을 공유해야한다.
- 각각의 클래스가 공유해야하는 부분을 위로 올리는 것에 집중해 추상화하자.

### 6.4.4 템플릿 메서드 패턴 사용하기
- 궁극적인 목표는 하위클래스가 메서드를 재정의하는 것을 통해 하위클래스만의 특수한 행동을 추가할 수 있도록 하기 위함
- 기본 구조를 상위클래스가 정의하고 상위클래스에서 메시지를 전송하여 하위클래스의 특수한 값을 덮는 기술을 템플릿 메서드(template method) 패턴이라고 부른다

### 6.4.5 모든 템플릿 메서드 구현하기
```ts
interface BicycleAttr {
  size?: string;
  chain?: string;
  tire_size?: number;
}

abstract class Bicycle<T extends BicycleAttr> {
  public readonly size: string;
  public readonly chain: string;
  public readonly tire_size: number;

  constructor(args: BicycleAttr) {
    this.size = args.size;
    this.chain = args.chain || this.default_chain;
    this.tire_size = args.tire_size || this.default_tire_size;
  }

  get default_chain(): string {
    return "10-speed";
  }

  get default_tire_size(): number {
    throw new Error("You have to implements get tire size");
  }
}


interface RoadBikeAttr extends BicycleAttr{
  tape_color?: string;
}

class RoadBike extends Bicycle<RoadBikeAttr> {
  private readonly tape_color: string;

  constructor(args: RoadBikeAttr) {
    super(args);
    this.tape_color = args.tape_color;
  }

  get default_tire_size(): number {
    return 23;
  }
}

interface MountainBikeAttr extends BicycleAttr{
  front_shock?: string;
  rear_shock?: string;
}

class MountainBike extends Bicycle<MountainBikeAttr> {
  private readonly front_shock: string;
  private readonly rear_shock: string;

  constructor(args:  MountainBikeAttr) {
    super(args);
    this.front_shock = args.front_shock;
    this.rear_shock = args.rear_shock;
  }

  get default_tire_size(): number {
    return 2.1;
  }
}
```

- 템플릿 메서드 패턴을 사용할 때는 언제나 호출되는 메서드를 직접 구현해 놓고 유용한 에러 메시지를 제공해야 한다.
- 하위 클래스가 메시지를 구현해야 한다고 명시적으로 말해주는 것은 그 자체로 훌륭한 문서가 된다.

## 6.5 상위클래스와 하위 클래스 사이의 커플링 관리하기

### 6.5.1 커플링 이해하기
```ts
abstract class Bicycle<T extends BicycleAttr> {
  // ...
  get spares(): T {
    return {
      chain: this.chain,
      tire_size: this.tire_size
    }
  }
}

class RoadBike extends Bicycle<RoadBikeAttr> {
  // ...
  get spares(): RoadBikeAttr {
    return {
      ...super.spares,
      tape_color: this.tape_color
    }
  }
}

class MountainBike extends Bicycle<MountainBikeAttr> {
  // ...
  get spares(): MountainBikeAttr {
    return {
      ...super.spares,
      rear_shock: this.rear_shock,
    }
  }
}
```
- Bicycle이 전송하는 모든 템플릿 메서드는 Bicycle 내에서 구현되어 있고 상속받은 클래스는 모두 super를 전송한다.
- 이 클래스에서 상속 관꼐는 잘 작동한다. 하지만 이들 모두 자기 자신에 대해 아는 것이 있고, 상위클래스에 대해 아는 것도 있다.
- 다른 클래스에 대해 알고 있다면 여기서 의존성이 만들어진다. 위 예시에서 의존성은 하위클래스가 super를 전송하면서 만들어진다.
- 하위클래스는 상위클래스와 어떻게 소통해야 하는지 알고 있어야한다. 하지만 상위클래스와 어떻게 소통해야 하는지도 알아야할 때, 문제가 발생한다.
- 하위클래스가 super를 전송한다는 것은 스스로 알고리즘에 대해 알고 있다고 말하는 것이며, 이 지식에 의존하고 있는 것이다.

### 6.5.2 훅 메시지를 사용해서 하위클래스의 결합 없애기
```ts
abstract class Bicycle<T> {
  constructor(args) {
    // ...
    this.postConstructor(args);
  }

  abstract postConstructor(args: T): void;

  public get spares(): BicycleAttr {
    return {
      chain: this.chain,
      tire_size: this.tire_size,
      ...this.local_spares,
    };
  }

  public get local_spares(): T {
    throw new Error("You have to implements get local spares");
  }
}

class RoadBike extends Bicycle<RoadBikeAttr> {
  // ...
  postConstructor(args: RoadBikeAttr): void {
    this.tape_color = args.tape_color;
  }

  public get local_spares(): RoadBikeAttr {
    return {
      tape_color: this.tape_color,
    };
  }
}

class MountainBike extends Bicycle<MountainBikeAttr> {
  // ...
  postConstructor(args: MountainBikeAttr): void {
    this.front_shock = args.front_shock;
    this.rear_shock = args.rear_shock;
  }

  public get local_spares(): MountainBikeAttr {
    return {
      front_shock: this.front_shock,
      rear_shock: this.rear_shock,
    };
  }
}
```
- 하위클래스가 알고리즘을 알고 있고 super를 전송하는 대신 훅(hook) 메시지를 전송할 수 있다.
- 훅 메시지는 정해진 메서드 구현을 통해 하위클래스가 정보를 제공할 수 있도록 만들어주는 메시지이다. 이 방법을 사용하면 하위클래스는 알고리즘에 대해 몰라도 되며, 상위클래스가 모든 권한을 가질 수 있게 된다.

## 6.6 요약
- 공통된 코드를 고립시키고 공통의 알고리즘을 추상 클래스가 구현할 수 있도록 해준다. 동시에 하위 클래스가 특수한 행동을 추가할 수 있는 여지도 남겨 놓는다
- 추상화된 상위클래스를 만드는 가장 좋은 방법은 구체적인 하위클래스의 코드를 위로 올리는 것이다.
- 추상화된 상위클래스는 템플릿 메서드 패턴을 이용해서 하위클래스가 자신의 특수한 내용을 추가할 수 있도록 돕는다.
- 훅 메서드를 이용해 하위클래스가 추상화 알고리즘을 알지 못해도 자신의 특수한 내용을 추가할 수 있다. 하위클래스가 super를 전송하지 않아도 괜찮기 때문에 결합이 느슨해진다.
