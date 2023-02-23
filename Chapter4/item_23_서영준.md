# Item23. 태그 달린 클래스보다는 클래스 계층구조를 활용하라

# 태그 달린 클래스?

```java
class Figure {
	enum Shape {RECTANGLE, CIRCLE};

	// 태그 필드 - 현재 모양을 나타낸다. **(상태를 나타내는 필드)**
	final Shape shape;

	// 다음 필드들은 모양이 사각형(RECTANGLE)일 때만 쓰인다.
	double length;
	double width;

	// 다음 필드는 모양이 원(CIRCLE)일 때만 쓰인다.
	double radius;

	// 원용 생성자
	Figure(double radius) {
		shape = Shape.CIRCLE;
		this.radius = radius;
	}

	// 사각형용 생성자
	Figure(double length, double width) {
		shape = Shape.RECTANGLE;
		this.length = length;
		this.width = width;
	}

	double area() {
		switch(shape) {
			case RECTANGLE: 
				return length * width;
			case CIRCLE:
				return Math.PI * (radius * radius);
			default:
				throw new AssertionError(shape);	
		}
	}
}
```

태그 달린 클래스란, **상태를 나타내는 필드를 가지고**, 필드의 상태에 따라 **역할이 달라지는 클래스**를 뜻합니다.

쉽게 말해 하나의 클래스에 **여러 역할이 동시에 부여된 클래스**라는 말이죠.

# 태그 달린 클래스의 단점

1. 쓸데없는 코드가 많다. (상태 필드, switch 문)
2. 여러 구현이 하나의 클래스에 혼합되어 있어서 가독성이 나쁘다.
3. 코드가 길어지니 메모리도 많이 잡아 먹는다.
4. 의미 없는 초기화를 해야 한다.
5. 새로운 역할을 추가하려면 코드를 수정해야 한다.
6. **인스턴스의 타입만으로는 의미를 파악할 수 없다. (의미를 파악하기 어렵고 잘 못된 행동을 하기 쉽다.)**

해결하기 위해 객체 지향 언어에서는 클래스 계층 구조를 지향해야한다.

# 클래스 계층구조를 만드는 방법

### 1. 계층 구조의 루트가 될 추상 크래스를 정의하고, 태그 값에 따라 동작이 달라지는 메서드들을 루트 클래스의 추상 메서드로 선언

루트가 될 **추상 클래스에 서로 공통으로 들어가는 부분을 작성**한다.

본 예제에서는 넓이를 구하는 `area() 메서드`가 공통으로 들어가기 때문에 이를 추상 메서드로 만들었다.

```java
abstract class Figure {
	abstract double area();
}
```

### 2. 태그 값에 상관없이 동작이 일정한 메서드들을 루트 클래스에 일반 메서드로 추가, 루트 클래스를 확장한 구체 클래스를 작성한다.

**역할에 따라 달라지는 부분을 각자 구현**한다.

```java
// Circle 구현체
class Circle extends Figure {
	final double radius;
	
	Circle(double radius) { this.radius = radius; }

	@Override double area() { return Math.PI * (radius * radius); }
}

// Rectangle 구현체
class Rectangle extends Figure {
	final double length;
	final double width;

	Rectangle(double length, double width) {
		this.length = length;
		this.width = width;
	}

	@Override double area() { return length * width; }
}
```

# 3. 효과

1. 상태를 나타내는 쓸데없는 코드들이 사라졌다.
2. 객체 생성 시 필요한 데이터만 초기화 하면 된다.
3. 타입 만으로 코드의 의미를 알 수 있다.
4. **코드를 수정하지 않고 새로운 의미(역할)을 부여할 수 있다. (수정보단 확장)**

<aside>
💡 핵심 정리 </br>
태그 달린 클래스를 써야 하는 상황은 거의 없다. 새로운 클래스를 작성하는 데 태그 필드가 등장한다면 태그를 없애고 계층구조로 대체하는 방법을 생각해보자. 
기존 클래스가 태그 필드를 사용하고 있다면 계층구조로 리팩터링하는 걸 고민해보자.

</aside>