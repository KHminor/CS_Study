### **디자인 패턴이란?**

\- 프로그램을 설계할 때 발생했던 문제점들을 객체 간의 상호 관계 등을 이용하여 해결할 수 있도록
 하나의 **규약** 형태로 만들어 놓은 것.

### **싱글톤 패턴이란?**

\- **하나의 클래스**에 오직 **하나의 인스턴스**만 가지는 패턴.
  하나의 클래스를 기반으로 여러 개의 개별적인 인스턴스를 만들 수는 있지만, 단 하나의 인스턴스를 만드는 패턴으로
  **중복되는 인스턴스 즉 같지 않은 인스턴스를 만들지 않는것으로**, 보통 데이터베이스 연결 모듈에 많이 사용.
\- 하나의 인스턴스를 만들어 놓고 해당 인스턴스를 다른 모듈들이 공유하며 사용하기 때문에
  **생성 비용이 줄어드는 장점**이 있지만, **의존성이 높아지는 단점**이 있다.

```javascript
// JavaScript Singleton Pattern

class Singleton {
  constructor() {
    if (!Singleton.instance) {
      Singleton.instance = this;
    }
  }
  getInstance() {
    return this.instance;
  }
}

const a = new Singleton();
const b = new Singleton();
```

Singleton.instance 라는 하나의 인스턴스를 가지는 Singleton 클래스.



```javascript
// Node.js의 MongoDB에서 사용하는 
// DB 연결시 사용하는 connect() 메서드가 사용하는 SingletonPattern

Mongoose.prototype.connect = function(url, options, callback) {
  const _mongoose = this instanceof Mongoose ? this : mongoose;
  const conn = _mongoose.connection;

  return _mongoose._promiseOrCallback(callback, cb => {
    conn.openUri(uri, options, err => {
      if (err != null) {
        return cb(err)
      }
      return cb(null, _mongoose)
    })
  })
}
```



### **싱글톤(Singleton) 패턴의 단점**

\- TDD(Test Driven Development)를 할 때는 단위 테스트를 주로 한다.
\- 단위 테스트는 테스트가 서로 독립적이어야 하며 테스트를 어떤 순서로든 실행할 수 있어야 하지만
  미리 생성된 하나의 인스턴스를 기반으로 구현하는 패턴이기에 **각 테스트마다 독립적인 인스턴스를 만들기 어렵다.
**- 또한 쉽고 실용적으로 사용할 수 있지만,
  모듈 간의 결합을 강하게 만들 수 있다는 단점이 있다. (모듈의 독립성 ↑ == (결합도 ↓ or 응집도 ↑)
\- 이때 의존성 주입(DI, Dependency Injection)을 통해 **모듈 간의 결합을 조금 더 느슨하게 만들어 해결**할 수 있다.
\- **`디커플링 된다`** 라고도 부른다.

\* **의존성?
**- 종속성이라고도 하며,
  A가 B에 있다는 것은 B의 변경 사항에 대한 영향력으로 A 또한 변해야 된다는 것을 의미. 



### **의존성 주입의 장점**

\- 모듈들을 쉽게 교체할 수 있는 구조가 되어 테스팅하기 쉬우며, 마이그레이션하기에도 수월하다.
\- 또한, 구현할 때 추상화 레이어를 넣고 이를 기반으로 구현체를 넣어 주기 때문에 애플리케이션
  의존성 방향이 일관되고, 애플리케이션을 쉽게 추론할 수 잇으며, 모듈 간의 관계들이 조금 더 명확해진다.



### **의존성 주입의 단점**

\- 모듈들이 더 많이 분리가 되므로 클래스 수가 늘어나 복잡성이 증가될 수 있기에 약간의 런타임 페널티가 생기기도 한다.



### **의존성 주입 원칙**

\- 상위 모듈은 하위 모듈에서 어떠한 것도 가져오지 않아야 한다.
\- 또한 둘 다 추상화에 의존해야 하며, 이때 추상화는 세부 사항에 의존하지 말아야 한다.



\# 면접을 위한 CS 전공지식 노트 공부