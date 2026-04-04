# Java 코딩 표준

## 1. 기본 원칙

이 코딩 표준은 가독성과 일관성을 최우선 가치로 삼는다.  
코드는 단순히 동작하는 것을 넘어, 읽는 사람이 빠르고 정확하게 의도를 이해할 수 있어야 한다.

- 가독성을 최우선으로 한다.
  - 코드는 주석 없이도 최대한 의도를 이해할 수 있어야 한다.
  - 복잡한 로직은 트릭보다 명확한 표현을 우선한다.
- 특별한 이유가 없는 한 IDE의 자동 서식 규칙을 따른다.
  - 이 문서는 IntelliJ IDEA의 기본 코드 포맷을 기준으로 한다.
  - 개인 취향보다 팀 전체의 일관성을 우선한다.
- 이름은 역할과 의도를 드러내야 한다.
  - 축약어, 애매한 약어, 임의의 줄임말보다 명확한 이름을 사용한다.
- 타입은 의미를 표현해야 한다.
  - 메서드의 매개변수와 반환값은 가능한 한 강타입으로 설계한다.
  - 문자열, 숫자, 불리언으로 의미를 암묵적으로 전달하는 설계를 지양한다.

이 코딩 표준은 [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)를 참고하되, 실제 개발 환경에서의 가독성과 유지보수성을 고려하여 일부 항목을 현실적으로 조정하였다.

## 2. 파일, 패키지, 클래스 구조

### 2.1. 패키지 규칙

- 패키지명은 모두 소문자로 작성한다.

```java
package com.awesome.shop.order;
```

### 2.2. import 규칙

- 와일드카드(`*`) import는 금지한다.
- 필요한 클래스만 명시적으로 import 한다.

```java
import com.awesome.shop.order.OrderRepository;
import com.awesome.shop.order.OrderService;
```

### 2.3. 클래스와 파일 구조

- 하나의 `.java` 파일에는 하나의 최상위 클래스만 둔다.
- 파일명은 클래스명과 정확히 일치해야 한다.

```java
public class OrderProcessor {
}
```

## 3. 네이밍 규칙

### 3.1. 클래스 / 인터페이스 / Enum

- 클래스와 Enum은 PascalCase를 사용한다.
- 인터페이스는 이름 앞에 `I` 접두어를 사용한다.
- 비트 플래그 용도의 Enum에는 `Flags` 접미사를 붙인다.
- Enum 멤버는 모두 대문자와 언더스코어를 사용한다.

```java
public class UserRegistrationService {
}

public interface IPaymentGateway {
}

public enum OrderStatus {
    PENDING,
    IN_PROGRESS,
    SHIPPED,
    COMPLETED
}
```

### 3.2. 메서드 이름 규칙

- 메서드 이름은 camelCase를 사용한다.
- 메서드 이름은 반드시 동사 또는 동사구로 시작한다.
- 가능하면 `동사 + 목적어` 형태로 작성한다.
- 반환값이 있는 메서드는 무엇을 반환하는지가 이름에 드러나야 한다.
- `boolean` 반환 메서드는 `is`, `has`, `can`, `should`를 우선 사용한다.
- 재귀 메서드는 이름 끝에 `Recursive`를 붙인다.

```java
public User findUserById(UserId userId);
public void processPayment();
public boolean isAuthenticated();
public boolean hasPermission();
public boolean canAccess();
public boolean shouldRetry();
public int calculateSumRecursive(int value);
```

### 3.3. 변수 이름 규칙

- 변수명은 camelCase를 사용한다.
- 변수명은 명사 또는 명사구를 사용한다.
- 값의 의미, 역할, 상태, 개수, 위치가 드러나야 한다.
- 의미 없는 축약어, 임의의 약어, 단일 문자 변수명은 사용하지 않는다.
  - 단, 짧은 반복문 인덱스처럼 관례적으로 명확한 경우는 예외로 한다.
- 컬렉션은 복수형으로 작성한다.
- `Map`은 가능하면 `xxxByYyy` 형식을 사용한다.
- `boolean` 변수는 `is`, `has`, `can`, `should`를 우선 사용한다.

```java
int retryCount;
int currentIndex;
String filePath;
List<User> users;
Map<UserId, User> usersById;
boolean isEnabled;
boolean hasPermission;
```

### 3.4. 멤버 변수 이름 규칙

- 인스턴스 멤버 변수는 `m` 접두어를 사용한다.
- static 변수는 `s` 접두어를 사용한다.
- 접두어 뒤에는 필드의 원래 이름을 대문자로 시작하여 이어 붙인다.

```java
private String mUserName;
private int mRetryCount;
private static String sDefaultTimeZone;
```

- 멤버 변수와 지역 변수, 매개변수는 이름만 보고도 구분 가능해야 한다.
- 멤버 변수에 `m` 접두어를 사용하므로, 일반적인 멤버 변수 접근에서는 `this`를 붙이지 않는다.
- `this`는 현재 객체 참조 자체가 필요한 경우에만 사용한다.

```java
private String mDisplayName;

public void changeDisplayName(String displayName) {
    mDisplayName = displayName;
}

public UserBuilder setDisplayName(String displayName) {
    mDisplayName = displayName;
    return this;
}
```

### 3.5. 상수 이름 규칙

- 상수는 모두 대문자와 언더스코어를 사용한다.

```java
public static final int MAX_RETRY_ATTEMPT = 5;
public static final String DEFAULT_TIME_ZONE = "Asia/Seoul";
```

## 4. 멤버 구성 및 순서

클래스 내부 구성은 다음 순서를 따른다.

1. 상수
2. static 필드
3. 인스턴스 필드
4. 생성자
5. public 메서드
6. default 메서드
7. protected 메서드
8. private 메서드

추가 원칙:

- 공개 메서드가 먼저 보이도록 배치한다.
- private 헬퍼 메서드는 이를 사용하는 public 메서드 아래쪽에 배치한다.
- 필드는 가능한 한 private으로 제한한다.

## 5. 접근 제어 및 캡슐화

- `public` 필드는 금지한다.
- 모든 필드는 원칙적으로 `private`으로 선언한다.
- 외부 노출이 필요하더라도 무조건 Getter / Setter를 만들지 않는다.
- 특히 Setter 남용을 피한다.
- 단순 값 변경보다 의도가 드러나는 메서드를 우선한다.

```java
private String mDisplayName;

public void changeDisplayName(String displayName) {
    mDisplayName = displayName;
}
```

- Getter는 정말 읽기 접근이 필요할 때만 제공한다.
- Setter는 외부에서 상태 변경 책임을 가져야 하는 경우에만 제한적으로 제공한다.

## 6. 변수 및 메서드 설계 규칙

### 6.1. 지역 변수 선언 규칙

- 지역 변수는 사용 직전에 선언하여 scope를 최소화한다.

```java
if (isValidRequest) {
    int totalPrice = calculateTotalPrice(orderItems);
    processTotalPrice(totalPrice);
}
```

- 지역 변수 이름은 같은 scope 안에서 역할이 중복되지 않아야 한다.
- 지역 변수 이름이 필드 이름과 겹치는 것은 허용하지 않는다.
  - 단, 생성자나 setter 계열 메서드의 매개변수는 예외로 한다.

### 6.2. 메서드 오버로딩 규칙

- 타입만 다른 모호한 오버로딩은 지양한다.
- 이름만으로도 동작 차이가 드러나야 한다.

```java
public void searchByUserId(UserId userId);
public void searchByKeyword(SearchKeyword searchKeyword);
```

### 6.3. 입력 검증 규칙

- 외부 입력은 시스템 경계에서 검증한다.
- 검증 실패 시 가능한 빨리 반환한다.
- 내부 메서드는 이미 검증된 값을 받는다고 가정한다.

```java
private final IUserRepository mUserRepository;

public User findUserByEmailOrNull(String emailOrNull) {
    if (emailOrNull == null || emailOrNull.isBlank()) {
        return null;
    }

    EmailAddress emailAddress = new EmailAddress(emailOrNull);
    return findUserByEmailInternal(emailAddress);
}

private User findUserByEmailInternal(EmailAddress emailAddress) {
    return mUserRepository.findByEmail(emailAddress);
}
```

### 6.4. 강타입 매개변수 설계 규칙

- 매개변수는 가능한 한 강타입으로 받는다.
- 의미를 암묵적으로 담은 `String`, `int`, `long`, `boolean`의 직접 사용을 지양한다.
- 정해진 선택지는 `enum`으로 표현한다.
- 도메인 의미가 있는 값은 전용 타입으로 감싸는 것을 우선 검토한다.
- 관련된 여러 매개변수가 함께 이동하면 별도의 객체로 묶는다.
- 의미가 다른 동일 타입 매개변수를 나란히 받는 설계를 피한다.
- `boolean` 매개변수는 호출부 가독성을 떨어뜨리므로 가급적 사용하지 않는다.

#### enum 사용 예

```java
public enum SortOrder {
    ASCENDING,
    DESCENDING
}

public void sortUsers(SortOrder sortOrder) {
    mUserRepository.sortByOrder(sortOrder);
}
````

#### 전용 타입 사용 예

```java
public final class UserId {
    private final long mValue;

    public UserId(long value) {
        mValue = value;
    }

    public long getValue() {
        return mValue;
    }
}

public final class SearchKeyword {
    private final String mValue;

    public SearchKeyword(String value) {
        mValue = value;
    }

    public String getValue() {
        return mValue;
    }
}

private final IUserRepository mUserRepository;

public User findUserById(UserId userId) {
    return mUserRepository.findById(userId);
}
```

#### parameter object 사용 예

```java
public final class OrderSearchCriteria {
    private final SearchKeyword mKeyword;
    private final OrderStatus mStatus;
    private final int mPageNumber;
    private final int mPageSize;

    public OrderSearchCriteria(SearchKeyword keyword, OrderStatus status, int pageNumber, int pageSize) {
        mKeyword = keyword;
        mStatus = status;
        mPageNumber = pageNumber;
        mPageSize = pageSize;
    }

    public SearchKeyword getKeyword() {
        return mKeyword;
    }

    public OrderStatus getStatus() {
        return mStatus;
    }

    public int getPageNumber() {
        return mPageNumber;
    }

    public int getPageSize() {
        return mPageSize;
    }
}

private final IOrderRepository mOrderRepository;

public List<Order> searchOrders(OrderSearchCriteria criteria) {
    return mOrderRepository.findByCriteria(criteria);
}
```

### 6.5. @Override 사용 규칙

- 메서드를 오버라이딩할 때는 항상 `@Override`를 명시한다.

```java
@Override
public User findUserById(UserId userId) {
    return mUserRepository.findById(userId);
}
```

## 7. 제어문 규칙

### 7.1. 중괄호 사용 규칙

- 모든 제어문에는 중괄호를 반드시 사용한다.
- 단일 실행문이어도 생략하지 않는다.

```java
if (isValidRequest) {
    proceed();
}
```

### 7.2. switch 문 규칙

- `default`는 항상 포함한다.
- 논리적으로 도달 불가능한 경우에도 방어용으로 작성한다.
- 의도적인 fall-through는 반드시 주석으로 명시한다.

```java
switch (orderStatus) {
    case PENDING:
        prepareShipping();
        // intentional fall-through
    case SHIPPED:
        notifyUser();
        break;
    default:
        assert false : "Unexpected status: " + orderStatus;
}
```

## 8. assert 사용 규칙

- assert는 논리적으로 절대 발생해서는 안 되는 상황을 검증할 때 사용한다.
- 반드시 괄호로 감싸서 작성한다.

```java
assert (denominator != 0) : "denominator must not be zero";
```

## 9. null 처리 규칙

- 매개변수로 `null`을 전달하는 것은 지양한다.
- `null`을 허용해야 하는 경우 매개변수 이름 끝에 `OrNull`을 붙인다.

```java
public User findUserByEmailOrNull(String emailOrNull);
```

- 반환값으로 `null`을 사용하는 것도 지양한다.
- `null`을 반환해야 하는 경우 메서드 이름 끝에 `OrNull`을 붙인다.

```java
public String getDisplayNameOrNull();
```

## 10. var 사용 규칙

- `var`는 타입이 우변에서 명확히 드러나는 경우에만 제한적으로 사용한다.
- 메서드 반환값처럼 타입이 이름만으로 분명하지 않은 경우에는 사용하지 않는다.

```java
var member = new Member();
var message = "Hello, World";
```

```java
User user = mUserService.findUserById(mCurrentUserId);
List<User> users = mUserRepository.findAll();
```

## 11. 포맷팅 규칙

### 11.1. 들여쓰기 및 중괄호 규칙

- IntelliJ 기본 포맷을 따른다.
- 다른 편집기 사용 시 탭 대신 공백 4칸을 사용한다.
- 여는 중괄호는 같은 줄에 둔다.
- 닫는 중괄호는 새로운 줄에 둔다.

```java
public void processData() {
    initialize();
    execute();
}
```

- `if`, `else if`, `else`는 다음과 같이 작성한다.

```java
if (condition) {
    processRequest();
} else if (otherCondition) {
    handleAlternativeCase();
} else {
    handleDefaultCase();
}
```

### 11.2. 수정자 순서 규칙

- 접근제어자는 다른 수정자보다 앞에 작성한다.

```java
public static void doSomething() {
}

private static final int RETRY_COUNT = 3;
```

### 11.3. 선언 규칙

- 한 줄에 변수 하나만 선언한다.

```java
int width = 0;
int height = 0;
int depth = 0;
```

### 11.4. 리터럴 표기 규칙

- `float` 리터럴에는 반드시 `f`를 붙인다.
- `long` 리터럴에는 반드시 대문자 `L`을 사용한다.

```java
float ratio = 0.25f;
long timeout = 1000L;
```