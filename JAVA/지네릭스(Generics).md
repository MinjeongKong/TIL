# 지네릭스(Generics)
## 지네릭스란?
지네릭스는 다양한 타입의 객체들을 다루는 메서드나 컬렉션 클래스에 컴파일 시의 타입체크(compile-time type check)를 해주는 기능이다. 객체의 타입을 컴파일 시에 체크하기 때문에 **객체의 타입 안정성을 높이고 형변환의 번거로움이 줄어든다.**

- 타입 안정성<br>의도하지 않은 타입의 객체가 저장되는 것을 막고, 저장된 객체를 꺼내올 때 원래의 타입과 다른 타입으로 잘못 형변환되어 발생할 수 있는 오류를 줄여주는 것

<br>

## 지네릭 클래스의 선언
지네릭 타입은 클래스와 메서드에 선언할 수 있다. 먼저 클래스에 선언하는 지네릭 타입에 대해 알아본다. 예를 들어 클래스 Box가 다음과 같이 정의되어 있다고 가정한다.
```java
class Box {
    Object item;

    void setItem(Object item) { this.item = item; }
    Object getItem() { return item; }
}
```
이 클래스를 지네릭 클래스로 변경하면 다음과 같이 클래스 옆에 '\<T>' 를 붙이면 된다. 그리고 'Object'를 모두 'T'로 바꾼다.
```java
class Box<T> {
    T item;

    void setItem(T item) { this.item = item; }
    T getItem() { return item; }
}
```

<br>

### 타입 변수의 표기

|타입|설명|예시
|---|---|---|
|\<T>|Type|Box\<T>|
|\<E>|Element|ArrayList\<E>|
|\<K>|Key|Map\<K,Y>|
|\<V>|Value|Map\<K,Y>|

무조건 'T'를 사용하기보다 가능하면 상황에 맞게 의미있는 문자를 선택해서 사용하는 것이 좋다. 이들은 **기호의 종류만 다를 뿐 '임의의 참조형 타입'을 의미하는 것은 모두 같다.**

기존에는 다양한 종류의 타입을 다루는 메서드의 매개변수나 리턴타입으로 Object타입의 참조변수를 많이 사용했고, 그로 인해 형변환이 불가피했지만, 이젠 Object타입 대신 원하는 타입을 지정하기만 하면 된다.

지네릭 클래스 객체를 생성할 때는 다음과 같이 참조변수와 생성자 타입 T대신에 사용될 실제 타입을 지정해주어야 한다.
```java
Box<String> b = new Box<String>(); //타입 T대신, 실제 타입을 지정
b.setItem(new Object()); //에러. String이외의 타입은 지정불가
b.setItem("ABC"); //OK. String타입이므로 가능
String item = (String) b.getItem(); // 형변환(String)이 필요없음
```

<br>

## 지네릭스의 제한
지네릭 클래스 Box의 객체를 생성할 때, 객체별로 다른 타입을 지정하는 것은 적절하다. 지네릭스는 이처럼 인스턴스별로 다르게 동작하도록 하려고 만든 기능이니까.
```java
Box<Apple> appleBox = new Box<Apple>(); //OK. Apple객체만 저장가능
Box<Grape> grapeBox = new Box<Grape>(); //OK. Grape객체만 저장가능
```
그러나 모든 객체에 대해 동일하게 동작해야하는 static멤버에 타입변수 T를 사용할 수 없다. T는 인스턴스변수로 간주되기 때문이다. static멤버는 인스턴스변수를 참조할 수 없다.
```java
class Box<T>{
    static T item; //에러
    static int compare(T t1, T t2){...} //에러
}
```
static멤버는 타입 변수에 지정된 타입, 즉 대입된 타입의 종류에 관계없이 동일한 것이어야 하기 때문이다. 즉, `Box<Apple>.item`과 `Box<Grape>.item`이 다른 것이어서는 안된다는 뜻이다.

지네릭 타입의 배열을 생성하는 것도 허용되지 않는다. 지네릭 배열 타입의 참조변수를 선언하는 것은 가능하지만, `new T[10]`과 같이 배열을 생성하는 것은 안 된다는 뜻이다.

```java
class Box<T>{
    T[] itemArr; //OK. T타입의 배열을 위한 참조변수
    ...
    T[] toArray(){
        T[] tmpArr = new T[itemArr.length]; //에러. 지네릭 배열 생성불가
        ...
        return tmpArr;
    }
}
```
지네릭 배열을 생성할 수 없는 것은 new 연산자 때문인데, 이 연산자는 **컴파일 시점에 타입 T가 뭔지 정확히 알아야 한다.** instanceof 연산자도 new 연산자와 같은 이유로 T를 피연산자로 사용할 수 없다.