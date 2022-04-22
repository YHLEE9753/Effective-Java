## toString을 재정의하라

> toString()은 Object 클래스의 메서드로 자바의 모든 객체는 Object 클래스를 상속한다.<br>
> 따라서 모든 객체는 toString() 메서드를 가지고 있습니다.

```java
// 오브젝트의 toString() 메서드
public String toString(){
    return getClass().getName() + "@" + Integer.toHexString(hashCode());   
}
```

### 사용용도
> 기본적으로 객체를 표현하는 문자열을 리턴해주기 때문에 개발할 때 사용됩니다.

1. 콘솔로 객체를 확인하는 경우
   1. System.out.println()
   2. 객체에 문자열을 연결(SomeObject + ")
   3. assert 구문 사용시
   ...
2. 디버깅 할 때
3. 로깅할 때

### 재정의 해야하는 이유
> 사용하는 예시가 print / 문자연결 / 디버깅 / 테스트 등 다양하지만 사용하는 주체가 개발자이다.
* **즉 개발자에게 객체가 보여지는데 Object toString() 메서드의 형태로 보여진다면 해당 객체 안에 뭐가 들었는지 알 수 없습니다.**
* 즉 사용하려는 객체에 toString()에 재정의하면 로깅 및 디버깅 시 개발자에게 좀 더 유익한 정보를 전해줄 수 있습니다.

### 재정의 하는 방법
* 재정의할 때 중요한건 객체 스스로를 완벽히 설명해야하는 문자열 형태여야 한다.
* 보통은 객체가 가진 주요정보를 모두 반환하는 것이 좋지만 객체가 거대하거나 객체의 상태가 문자열로 표현하기에 적합하지 않다면 무리가 있다.
* 각 클래스이 가진 데이터에 따라 자동 생성 toString이 적합할 수도 있고 그렇지 않을 수도 있다
* 전화번호 처럼 포맷이 정해져 있는 경우 아래 코드처럼 주석을 달아 문서화를 해줄 수도 있습니다.
```java
/**
 * 이 전화번호의 문자열 표현을 반환한다.
 * 이 문자열은 "XXX-YYY-ZZZZ" 형태의 12글자로 구성된다.
 * XXX는 지역코드 YYY는 프리픽스 ZZZZ는 가입자 번호다.
 * 각각의 대문자는 10진수 숫자 하나를 나타낸다.
 * 
 * 전화번호의 각 부분의 값이 너무 작아서 자릿수를 채울 수 없다면, 
 * 앞에서부터 0으로 채워나간다. 예컨대 가입자 번호가 123이라면
 * 전화번호의 마지막 네 문자는 "0123"이 된다.
 */
@Override public String toString(){
    return String.format("%03d-%03d-%04d", areaCode, prefix, lineNum);        
}
```
* 다만 포맷을 정해준다면 앞으로의 유지보수에도 항상 이 포맷을 사용해야 하기 때문에 신중히 포맷을 정할 필요가 있습니다.
```java
@Override
public String toString() {
    return "PhoneNumber{" +
        "areaCode='" + areaCode + '\'' +
        ", prefix='" + prefix + '\'' +
        ", lineNum='" + lineNum + '\'' +
        '}';
}
```
* 유틸리티 클래스는 toString()을 사용할 필요가 없고, Enum은 아래처럼 toString() 이미 정의되어 있으므로 재정의 하지 않아도 된다.

### 결론
* Object의 클래스의 toString 메서드는 클래스이름과 해시코드가 있는 문자열이 나오게된다.
* 클래스 이름과 해시코드가 있는 문자열이 나오게 된다면 알아보기가 힘들다 위의 내용처럼 toString을 사용하는 주체는 개발자 이기 때문에 재정의해서 알아보기 쉽게 만들자.
* toString에서 순환참조를 주의하자.

<hr>

## 질문

### 용훈
* toString 으로 포맷을 명시하면 추후 포맷 변경시 코드들과 데이터들이 엉망이 될것이라고 써있는데 실제로 프로젝트를 진행하면서 이러한 부분에서 문제가 발생하는 사례가 있을까요?
* 문제가 된다면 변경가능성이 많은 프로젝트의 경우 포맷을 명시하지 않아 유연성을 확보하는게 유리할까요?

> 프로젝트를 진행하면서 문제가 발생했던 사례는 없지만 IDE의 도움을 받은게 아닌 직접 포맷을 명시하여 toString을 재정의하는 경우에
> <br> 직접 재정의한 객체에 대해 정보가 필요할 경우 사용하는 곳에서 현재 상테에 맞게 파싱하는 작업을 하는 과정에서 만약 포맷이나 형식이 바뀐다면 
> <br> 해당 객체를 사용하고 있는 곳에서 모두 해당 포맷에 맞게 수정작업이 일어나기 때문에 코드들이 엉망이 될 수 있지 않을까 생각을 해봤습니다. 
> <br> 포맷은 변했는데 수정을 하지 않았다면 기존에 맞춰놨던 파싱작업에 영향이 가면서 시스템에 예외가 발생할 수도 있을것 같습니다. 

<hr>

### 병연
* toString에서 순환참조는 양방향의 의존성을 가질 경우를 말씀하시는 건지 궁금합니다.

> 네 맞습니다. 특히 롬복의 @ToString을 사용할 경우에도 모든 필드에 대한 정보가 생기기 때문에 더욱 조심해야 합니다. 
> 
>그렇기 때문에 그러한 서로 참조하고 있는 관계들은 끊어줘야합니다.

<hr>

### 연우
* 두 메소드가 같은 값을 리턴하고 있는 모습이지만 자바 공식 문서에서는 name() 보다는 toString() 을 사용할 것은 권하고 있습니다. 그 이유는 무엇일까요?

> name과 toString의 처음 형태는 동일한 값을 보여주지만 toString은 override를 통해 재정의를 할 수 있습니다. 
> 따라서 더욱 사용자나 개발자 입장에서 보기 쉽게 나타내줄 수 있기 때문에 toString을 권하는 것 같습니다.

* 열거타입의 경우 어떤 때 toString() 을 재정의 하는 것이 좋을까요?

> 만약 요일을 enum타입으로 정의하고 첫글자만 대문자로 하고싶다면?
> * 출처 : https://stackoverflow.com/questions/18031125/what-is-the-difference-between-enum-name-and-enum-tostring

```java
public enum WeekDay{
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
    
    public String toString() {
       return name().charAt(0) + name().substring(1).toLowerCase();
    }
}

// name을 통해 가져올 경우
WeekDay.MONDAY.name() // "MONDAY"

// toString을 재정의 한 경우
WeekDay.MONDAY.toString() // "Monday"
```

<hr>

### 수미
* 객체의 ID 값은 toString으로 반환해주는것이 좋을까요?
* 개인적으로 UUID 값은 형태가 복잡해서 출력하지 않는게 좋을 것 같다가도, DB관리자 등이 ID 값을 참조해야 하는 경우에는 출력이 필요할 수도 있을것 같은데 다른 분들의 의견이 궁금합니다.

> 저는 개인적으로 이번 아이템12의 내용을 어느정도 동의는 하지만, 모든 클래스에 toString을 재정의하며 모든 정보를 포함시키는 건 안된다고 생각하고 있습니다. <br>
> toString은 결국 공개되어있는 정보이기 때문에 "공개 되어도 괜찮은"정보만 포함시켜야 한다고 생각합니다 ! 예를들어 DTO 같은 경우가 있을 것 같아요 <br>
> 즉 노출되면 안되는 고유한 아이디 값들이나 민감한 정보들은 제외해야 한다고 생각을 해봅니다! <br>