싱글턴은 만드는 방식은 보통 둘 중 하나이다. 두 방식 모두 생성자는 private으로 감춰두고, 인스턴스에 접근할 수 있는 수단으로 public static 멤버를 마련해둔다.

첫번째 방식은, **public static final필드 방식**이다.

# public static final field로 생성

```
public class Elvis {
    public static final Elvis INSTANCE = new Elvis(5);
    public int age;
    private Elvis(int age) {
        this.age = age;
    }
}


public class Main {
    public static void main (String[] args){

        Elvis elvis = Elvis.INSTANCE; //외부 클래스에서 접근 방식
        System.out.println(elvis); //Elvis@2a139a55

        Elvis elvis2 = Elvis.INSTANCE;
        System.out.println(elvis2); //Elvis@2a139a55

    }
}
```

두번째는 **정적 팩터리 방식의 싱글턴**이다.

# 정적 팩토리 방식의 싱글톤 생성

```
public class Elvis {
    private static final Elvis INSTANCE = new Elvis(5);
    public int age;
    private Elvis(int age){ this.age = age ;}
    public static Elvis getInstance(){
        return INSTANCE;
    }
}


public class Main {
    public static void main (String[] args){

        Elvis elvis = Elvis.getInstance(); // 외부 클래스에서 호출 방법
        System.out.println(elvis); //Elvis@2a139a55

        Elvis elvis2 = Elvis.getInstance();
        System.out.println(elvis2); //Elvis@2a139a55

    }
}
```

위의 두가지 방법으로 만든 싱글턴 클래스를 직렬화하려면 단순히 Serializable을 구현하는 것으로는 부족한다. 모든 인스턴스 필드를 일시적(transient)이라고 선언하고 readResolve 메소드를 제공해야 한다. 이렇게 하지 않으면 인스턴스를 역직렬화 할 때마다 새로운 인스턴스가 생성된다. 따라서 Elvis의 클래스에 싱글턴임을 보장해주는 readResolve 메소드를 추가해주어야 한다.

[https://madplay.github.io/post/java-serialization](https://madplay.github.io/post/java-serialization)


```
private Object readResolve(){
	return INSTANCE;
}
```

마지막으로는 **열거타입**이다.

# 열거타입으로 싱글톤 생성
```
public enum Elvis {
    INSTANCE;
}

public class Main {
    public static void main (String[] args){
        Elvis elvis = Elvis.INSTANCE;
        System.out.println(elvis.toString());
    }
}
```
