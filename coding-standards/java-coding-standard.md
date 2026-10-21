# Java 코딩 표준

## 1. 기본 원칙

이 코딩 표준은 가독성과 일관성을 최우선 가치로 삼는다.

- 코드의 의도가 이름과 구조에 드러나게 작성한다.
- 복잡한 기법보다 명확한 표현을 우선한다.
- 자동 서식은 프로젝트의 서식 설정을 따른다.
- 같은 개념에는 같은 이름을 사용한다.
- 자료형으로 값의 의미를 표현한다.
- 실패 조건은 빠르고 분명하게 처리한다.

참고 자료:

- [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)

## 2. 파일, 패키지, 클래스 구조

### 2.1. 파일 구성 순서

Java 소스 파일은 다음 순서로 구성한다.

1. 라이선스 또는 저작권 주석
2. `package` 선언
3. `import` 선언
4. 최상위 클래스, 인터페이스 또는 열거형 선언

각 영역 사이에는 빈 줄 하나를 둔다.

```java
package com.awesome.shop.order;

import java.util.List;

public class OrderService {
    private final List<Order> mPendingOrders;

    public OrderService(List<Order> pendingOrders) {
        mPendingOrders = pendingOrders;
    }
}
```

### 2.2. 패키지 규칙

- 패키지 이름은 모두 소문자로 작성한다.
- 회사, 제품, 도메인, 기능 순서처럼 큰 범위에서 작은 범위로 내려가도록 작성한다.
- 패키지 이름에 밑줄(`_`)이나 대문자를 사용하지 않는다.

좋지 않은 예:

```java
package com.awesome.Shop.Order;
package com.awesome.shop_order;
```

좋은 예:

```java
package com.awesome.shop.order;
package com.awesome.shop.payment;
package com.awesome.shop.delivery.tracking;
```

### 2.3. `import` 규칙

- 와일드카드(`*`) `import`는 금지한다.
- 사용하지 않는 `import`는 남기지 않는다.
- `static import`는 테스트 코드나 가독성이 분명히 좋아지는 경우에만 제한적으로 사용한다.

좋지 않은 예:

```java
import java.time.*;
import java.util.*;
```

좋은 예:

```java
import java.time.LocalDate;
import java.util.List;

import com.awesome.shop.payment.PaymentGateway;
import com.awesome.shop.user.UserRepository;
```

### 2.4. 클래스와 파일 구조

- 파일 분리는 줄 수보다 책임과 응집도를 기준으로 결정한다.
- 독립적인 책임은 별도 타입으로 분리한다.
- 하나의 `.java` 파일에는 하나의 최상위 클래스, 인터페이스 또는 열거형만 둔다.
- 파일 이름은 최상위 선언 이름과 정확히 일치해야 한다.
- 작은 보조 클래스가 필요하면 중첩 클래스로 두거나 별도 파일로 분리한다.

```java
public class OrderProcessor {
}
```

```text
OrderProcessor.java
```

## 3. 네이밍 규칙

### 3.1. 클래스, 인터페이스, 열거형 이름 규칙

- 클래스는 `PascalCase`를 사용한다.
- 클래스 이름은 명사 또는 명사구로 작성한다.
- 인터페이스는 `PascalCase`를 사용하며 이름 앞에 `I` 접두어를 사용한다.
- 열거형은 `PascalCase`를 사용하며 이름 앞에 `E` 접두어를 사용한다.
- 비트 플래그나 권한 조합처럼 플래그 성격의 열거형에는 `Flags` 접미사를 붙인다.
- 열거형 멤버는 모두 대문자와 밑줄을 사용한다.
- 열거형으로 정의한 플래그 조합은 `EnumSet`으로 표현한다.

좋지 않은 예:

```java
public interface PaymentGateway {
}

public enum OrderStatus {
    pending,
    paid
}
```

좋은 예:

```java
public class UserRegistrationService {
}

public interface IPaymentGateway {
}

public enum EOrderStatus {
    PENDING,
    PAID,
    READY_TO_SHIP,
    SHIPPED,
    CANCELED
}

public enum EPermissionFlags {
    READ,
    WRITE,
    DELETE
}
```

플래그 조합:

```java
EnumSet<EPermissionFlags> permissions = EnumSet.of(EPermissionFlags.READ, EPermissionFlags.WRITE);
```

### 3.2. 메서드 이름 규칙

- 메서드 이름은 `camelCase`를 사용한다.
- 메서드 이름은 반드시 동사 또는 동사구로 시작한다.
- 반환값이 있는 메서드는 무엇을 반환하는지가 이름에 드러나야 한다.
- `boolean` 반환 메서드는 `is`, `has`, `can`, `should`를 우선 사용한다.
  - 이 표현이 부자연스러운 경우에는 상태를 나타내는 다른 3인칭 단수형 동사를 사용한다.
- 재귀 메서드 이름에는 `Recursive` 접미사를 붙인다.

좋지 않은 예:

```java
public User user(UserId userId);
public void process();
public boolean flag();
public BigDecimal calculate(OrderNode orderNode);
```

좋은 예:

```java
public User findUserById(UserId userId);
public List<Order> findPendingOrders(UserId userId);
public void approvePayment(PaymentId paymentId);
public boolean isExpired(Coupon coupon);
public boolean hasPermission(User user, EPermissionFlags permission);
public boolean canCancel(Order order);
public boolean shouldRetry(PaymentAttempt paymentAttempt);
public BigDecimal calculateOrderTotalRecursive(OrderNode orderNode);
```

### 3.3. 변수 이름 규칙

- 변수 이름은 `camelCase`를 사용한다.
- 변수 이름은 명사 또는 명사구를 사용한다.
- 값의 의미, 역할, 상태, 개수, 위치가 드러나야 한다.
- 의미 없는 축약어, 임의의 약어, 단일 문자 변수 이름은 사용하지 않는다.
  - 단, 짧은 반복문 인덱스처럼 관례적으로 명확한 경우는 예외로 한다.
- 컬렉션은 복수형으로 작성한다.
- `Map` 같은 키-값 대응 컬렉션은 가능하면 `xxxByYyy` 형태를 사용한다.
- `boolean` 변수는 `is`, `has`, `can`, `should`를 우선 사용한다.

좋지 않은 예:

```java
int cnt;
String str;
List<Order> list;
Map<OrderId, Order> map;
boolean flag;
```

좋은 예:

```java
int retryCount;
int currentPage;
String invoiceFilePath;

List<Order> pendingOrders;
Map<OrderId, Order> ordersById;

boolean isPaymentApproved;
boolean hasDeliveryAddress;
boolean canRetry;
boolean shouldUpdateCache;
```

### 3.4. 멤버 변수 이름 규칙

- 인스턴스 멤버 변수는 앞에 `m`을 붙이고 `PascalCase`를 사용한다.
- `static` 멤버 변수는 앞에 `s`를 붙이고 `PascalCase`를 사용한다.
- `boolean` 멤버 변수는 `mIs`, `mHas`, `mCan`, `mShould` 형태를 우선 사용한다.
- 상수는 `s` 접두어를 사용하지 않고 3.5의 상수 이름 규칙을 따른다.

좋은 예:

```java
private String mUserName;
private int mRetryCount;
private boolean mIsEnabled;
private boolean mHasPermission;
private static String sCurrentTimeZone;
```

#### `this` 사용 규칙

- 멤버 변수에 `m` 접두어를 사용하므로, 일반적인 멤버 변수 접근에서는 `this`를 붙이지 않는다.
- `this`는 현재 객체 참조 자체가 필요하거나 `this(...)`로 다른 생성자에 위임할 때 사용한다.

좋지 않은 예:

```java
private String displayName;

public void changeDisplayName(String displayName) {
    this.displayName = displayName;
}
```

좋은 예:

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

- 상수는 모두 대문자와 밑줄을 사용한다.
- 공개하는 상수 객체는 내부 상태도 변경할 수 없도록 설계한다.

좋지 않은 예:

```java
public static final int MAX_RETRY = 5;
public static final long TIMEOUT = 3000L;
```

좋은 예:

```java
public static final int MAX_RETRY_ATTEMPTS = 5;
public static final long PAYMENT_TIMEOUT_MILLIS = 3_000L;
public static final String DEFAULT_TIME_ZONE = "Asia/Seoul";
```

## 4. 멤버 구성 및 순서

클래스 내부 구성은 다음 순서를 따른다.

1. 중첩 타입
2. 상수
3. `static` 멤버 변수
4. 인스턴스 멤버 변수
5. 생성자
6. `public` 메서드
7. `protected` 메서드
8. package-private 메서드
9. `private` 메서드

추가 원칙은 다음과 같다.

- 관련된 멤버는 위의 구성 순서를 유지하면서 논리적으로 묶는다.
- 같은 접근 수준의 오버로드된 메서드는 서로 떨어뜨리지 않고 함께 배치한다.

좋은 예:

```java
public class OrderService {
    public static final int MAX_ORDER_LINE_COUNT = 100;

    private static String sCurrentCurrencyCode = "KRW";

    private final IOrderRepository mOrderRepository;
    private final IPaymentGateway mPaymentGateway;

    public OrderService(IOrderRepository orderRepository, IPaymentGateway paymentGateway) {
        mOrderRepository = orderRepository;
        mPaymentGateway = paymentGateway;
    }

    public Receipt approveOrder(OrderId orderId) {
        Order order = findOrderOrNull(orderId);
        if (order == null || !order.canApprovePayment()) {
            return Receipt.rejected(orderId);
        }

        mPaymentGateway.approve(order.getPaymentId());
        order.markAsPaid();
        mOrderRepository.save(order);
        return Receipt.approved(orderId);
    }

    private Order findOrderOrNull(OrderId orderId) {
        return mOrderRepository.findByIdOrNull(orderId);
    }
}
```

## 5. 접근 제어 및 캡슐화

### 5.1. 멤버 접근 제어 규칙

- `public` 인스턴스 멤버 변수와 변경 가능한 `public static` 멤버 변수는 금지한다.
- 상수를 제외한 모든 멤버 변수는 원칙적으로 `private`으로 선언한다.
- 외부 노출이 필요하더라도 일괄적으로 getter와 setter를 만들지 않는다.
- 단순 값 변경보다 의미 있는 상태 변경을 표현하는 메서드를 우선한다.

좋지 않은 예:

```java
public class OrderSummary {
    public int mCompletedOrderCount;
}
```

좋은 예:

```java
public class OrderSummary {
    private int mCompletedOrderCount;

    public int getCompletedOrderCount() {
        return mCompletedOrderCount;
    }

    public void addCompletedOrder() {
        ++mCompletedOrderCount;
    }
}
```

### 5.2. getter와 setter 사용 규칙

- getter는 외부 읽기 접근이 필요할 때만 제공한다.
- setter는 외부에서 상태 변경 책임을 가져야 하는 경우에만 제한적으로 제공한다.

좋은 예:

```java
public class UserProfile {
    private String mDisplayName;
    private boolean mHasMarketingConsent;

    public UserProfile(String displayName) {
        mDisplayName = displayName;
    }

    public String getDisplayName() {
        return mDisplayName;
    }

    public boolean changeDisplayName(String displayNameOrNull) {
        if (displayNameOrNull == null || displayNameOrNull.isBlank()) {
            return false;
        }

        mDisplayName = displayNameOrNull;
        return true;
    }

    public void grantMarketingConsent() {
        mHasMarketingConsent = true;
    }
}
```

### 5.3. 읽기 전용 값 규칙

- 생성 후 값이 바뀌지 않는 멤버 변수는 `final`로 선언한다.
- `final`은 필드의 재대입만 막는다.
  - 객체 전체를 불변으로 유지하려면 참조 대상의 변경도 막는다.
- 필수 의존성은 생성자에서 받고 `final` 멤버 변수에 저장한다.

좋은 예:

```java
public class OrderService {
    private final IOrderRepository mOrderRepository;
    private final IPaymentGateway mPaymentGateway;

    public OrderService(IOrderRepository orderRepository, IPaymentGateway paymentGateway) {
        mOrderRepository = orderRepository;
        mPaymentGateway = paymentGateway;
    }
}
```

## 6. 변수 및 메서드 설계 규칙

### 6.1. 지역 변수 선언 규칙

- 지역 변수는 필요한 시점에 선언하고 선언과 동시에 의미 있는 값으로 초기화한다.
- 같은 범위 안에서는 지역 변수마다 서로 다른 역할이 이름에 분명히 드러나야 한다.

좋지 않은 예:

```java
int amount;

if (order.hasDiscountCoupon()) {
    amount = calculateDiscountAmount(order);
    order.applyDiscount(amount);
}
```

좋은 예:

```java
if (order.hasDiscountCoupon()) {
    int discountAmount = calculateDiscountAmount(order);
    order.applyDiscount(discountAmount);
}
```

### 6.2. 복잡한 표현식 분리 규칙

- 복잡한 표현식은 의미 있는 계산 단위로 나누어 지역 변수에 담는다.
- 중간 변수 이름은 계산 방식보다 값의 의미를 드러내야 한다.
- 단순히 줄 길이를 줄이기 위한 `temp`, `value`, `result` 같은 이름은 사용하지 않는다.
- 한눈에 이해되는 단순 표현식은 불필요하게 나누지 않는다.

좋지 않은 예:

```java
int sourceByteIndex = (region.getY() + y) * bytesPerRow + (region.getX() + x) * channelCount + channelIndex;
```

좋은 예:

```java
int sourceRowOffset = (region.getY() + y) * bytesPerRow;
int sourceColumnOffset = (region.getX() + x) * channelCount;

int sourceByteIndex = sourceRowOffset + sourceColumnOffset + channelIndex;
```

### 6.3. 메서드 오버로딩 규칙

- 메서드 오버로딩은 의미가 같고 입력 형태만 다를 때만 사용한다.
- 의미가 다른 메서드는 이름을 분리한다.

좋지 않은 예:

```java
public User search(UserId userId);
public List<User> search(SearchKeyword searchKeyword);
```

좋은 예:

```java
public User findUserById(UserId userId);
public List<User> searchUsersByKeyword(SearchKeyword searchKeyword);
```

### 6.4. 변수 가리기 금지

- 안쪽 범위의 이름이 바깥쪽 범위의 이름을 가리지 않게 한다.
- 멤버 변수와 매개 변수의 이름이 충돌하지 않게 한다.
- IDE 또는 정적 분석 도구에서 변수 가리기를 감지할 수 있으면 경고를 활성화한다.

좋지 않은 예:

```java
public class OrderService {
    private IOrderRepository mOrderRepository;

    public OrderService(IOrderRepository mOrderRepository) {
        mOrderRepository = mOrderRepository;
    }
}
```

좋은 예:

```java
public class OrderService {
    private final IOrderRepository mOrderRepository;

    public OrderService(IOrderRepository orderRepository) {
        mOrderRepository = orderRepository;
    }
}
```

### 6.5. 강타입 매개 변수 설계 규칙

- 매개 변수는 가능한 한 강타입으로 받는다.
- 의미를 암묵적으로 담은 `String`, `int`, `long`, `boolean`의 직접 사용을 지양한다.
- 정해진 선택지는 `enum`으로 표현한다.
- 도메인 의미가 있는 값은 전용 자료형으로 감싸는 것을 우선 검토한다.
- 전용 자료형은 가능한 한 불변으로 설계한다.
- 값 객체는 값에 따른 동등성을 제공하며, `equals()`와 `hashCode()`를 함께 재정의한다.
- 전용 자료형과 매개 변수 객체의 유효성은 생성 시 또는 시스템 경계에서 검증한다.
- 관련된 여러 매개 변수는 별도의 객체로 묶는 것을 우선 검토한다.
- 의미가 다른 동일 자료형 매개 변수를 나란히 받는 설계를 피한다.
- `boolean` 매개 변수는 호출부 가독성을 떨어뜨리므로 가능한 한 사용하지 않는다.

#### 열거형 사용 예

```java
public enum ESortOrder {
    ASCENDING,
    DESCENDING
}

public List<User> sortUsers(List<User> users, ESortOrder sortOrder) {
    return mUserRepository.sortByOrder(users, sortOrder);
}
```

#### 전용 자료형 사용 예

```java
public final class UserId {
    private final long mValue;

    private UserId(long value) {
        mValue = value;
    }

    public static UserId createOrNull(long value) {
        if (value <= 0) {
            return null;
        }

        return new UserId(value);
    }

    public long getValue() {
        return mValue;
    }

    @Override
    public boolean equals(Object objectOrNull) {
        if (!(objectOrNull instanceof UserId)) {
            return false;
        }

        UserId otherUserId = (UserId) objectOrNull;
        return mValue == otherUserId.mValue;
    }

    @Override
    public int hashCode() {
        return Long.hashCode(mValue);
    }
}

private final IUserRepository mUserRepository;

public User findUserById(UserId userId) {
    return mUserRepository.findById(userId);
}
```

#### 매개 변수 객체 사용 예

```java
public final class OrderSearchCriteria {
    public static final int MAX_PAGE_SIZE = 100;

    private final SearchKeyword mKeyword;
    private final EOrderStatus mStatus;
    private final int mPageNumber;
    private final int mPageSize;

    private OrderSearchCriteria(SearchKeyword keyword, EOrderStatus status, int pageNumber, int pageSize) {
        mKeyword = keyword;
        mStatus = status;
        mPageNumber = pageNumber;
        mPageSize = pageSize;
    }

    public static OrderSearchCriteria createOrNull(
            SearchKeyword keywordOrNull,
            EOrderStatus statusOrNull,
            int pageNumber,
            int pageSize) {
        if (keywordOrNull == null || statusOrNull == null) {
            return null;
        }
        if (pageNumber < 1) {
            return null;
        }
        if (pageSize < 1 || pageSize > MAX_PAGE_SIZE) {
            return null;
        }

        return new OrderSearchCriteria(keywordOrNull, statusOrNull, pageNumber, pageSize);
    }

    public SearchKeyword getKeyword() {
        return mKeyword;
    }

    public EOrderStatus getStatus() {
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
    return mOrderRepository.search(criteria);
}
```

#### `boolean` 매개 변수 지양 예

좋지 않은 예:

```java
public void saveOrder(Order order, boolean shouldOverwrite);
```

좋은 예:

```java
public enum ESaveMode {
    CREATE_NEW,
    OVERWRITE
}

public void saveOrder(Order order, ESaveMode saveMode);
```

### 6.6. 컬렉션 자료형 사용 규칙

- 원시 타입(raw type) 컬렉션은 사용하지 않는다.
- 일반 배열을 사용하는 것도 허용한다.
- 공개 API에는 필요한 기능을 표현하는 컬렉션 인터페이스를 우선한다.
- 외부에서 변경하면 안 되는 컬렉션은 변경 가능한 내부 컬렉션을 그대로 노출하지 않는다.
- 반환한 목록의 변경을 허용하지 않으면 변경 불가 컬렉션을 제공한다.
  - `List<T>` 자체는 읽기 전용 접근을 보장하지 않는다.
  - 변경 불가 컬렉션도 요소 전체의 불변성을 보장하지 않는다.

좋은 예:

```java
List<Order> completedOrders = new ArrayList<Order>();
Map<UserId, User> usersById = new HashMap<UserId, User>();
String[] fileNames = new String[fileCount];
```

### 6.7. 입력 검증 및 오류 처리 규칙

- 외부 입력은 시스템 경계에서 검증한다.
- 검증 실패는 시스템 경계에서 즉시 처리한다.
- 내부 메서드는 이미 검증된 값을 받는다고 가정한다.
- 내부 메서드의 사전 조건이 깨지면 즉시 실패가 드러나게 한다.

- 예상 가능한 실패는 `OrNull` 접미사가 있는 메서드나 전용 결과 타입처럼 호출자가 처리할 수 있는 형태로 표현한다.
- 실패 여부를 반환하는 호출은 결과를 확인한 뒤 후속 처리를 진행한다.
- 같은 모듈에서는 일관된 오류 처리 방식을 사용한다.
- 예외를 잡고 아무 처리 없이 무시하지 않는다.

좋은 예:

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
    assert (emailAddress != null) : "emailAddress must already be validated";
    return mUserRepository.findByEmail(emailAddress);
}
```

### 6.8. `@Override` 사용 규칙

- 메서드를 재정의하거나 인터페이스 메서드를 구현할 때는 `@Override`를 명시한다.

```java
@Override
public User findUserById(UserId userId) {
    return mUserRepository.findById(userId);
}
```

## 7. 제어문 규칙

### 7.1. 빠른 반환 규칙

- 실패 조건이나 중단 조건은 앞에서 처리한다.
- 정상 흐름이 깊은 중첩 안에 들어가지 않도록 한다.
- 빠른 반환을 사용할 때도 반환값의 의미가 분명해야 한다.

좋은 예:

```java
public Receipt approvePayment(Order order) {
    if (!order.canApprovePayment()) {
        return Receipt.rejected(order.getId());
    }

    Payment payment = mPaymentGateway.approve(order.getPaymentId());
    return Receipt.approved(order.getId(), payment.getApprovedAt());
}
```

### 7.2. `switch` 문 규칙

- `switch` 문에는 언제나 `default`를 작성한다.
- 처리할 수 없는 값은 `default`에서 assertion으로 표시한다.
- 의도적인 fall-through는 반드시 주석으로 명시한다.

좋지 않은 예:

```java
switch (orderStatus) {
    case PAID:
        prepareShipping();
    case READY_TO_SHIP:
        createShipment();
        break;
}
```

좋은 예:

```java
public void handleOrderStatus(EOrderStatus orderStatus) {
    switch (orderStatus) {
        case PENDING:
            requestPayment();
            break;
        case PAID:
            prepareShipping();
            // intentional fall-through
        case READY_TO_SHIP:
            createShipment();
            break;
        case SHIPPED:
            notifyShipmentCompleted();
            break;
        case CANCELED:
            closeOrder();
            break;
        default:
            assert (false) : "Unexpected order status: " + orderStatus;
    }
}
```

### 7.3. 반복문 규칙

- 컬렉션 전체를 순회할 때는 향상된 `for` 문을 우선한다.
- 인덱스가 필요한 경우에만 전통적인 `for` 문을 사용한다.
- 반복 중 컬렉션을 수정해야 한다면 `Iterator` 등 안전한 수정 방식을 사용한다.

좋은 예:

```java
for (Order order : orders) {
    submitOrder(order);
}

for (int index = 0; index < orders.size(); ++index) {
    updateOrderPosition(orders.get(index), index);
}
```

### 7.4. 람다와 스트림 사용 규칙

- 람다는 짧고 지역적인 동작에만 사용한다.
- 여러 작업을 수행하는 람다는 메서드나 명시적인 반복문으로 분리한다.
- 부수 효과가 있는 작업은 복잡한 스트림 체인보다 명시적인 반복문을 우선한다.

좋지 않은 예:

```java
orders.forEach(order -> {
    reserveInventory(order);
    sendOrderConfirmationEmail(order);
});
```

좋은 예:

```java
for (Order order : orders) {
    reserveInventory(order);
    sendOrderConfirmationEmail(order);
}
```

### 7.5. 리소스 해제 규칙

- `AutoCloseable` 리소스는 `try-with-resources`로 수명 범위를 드러낸다.
- 리소스를 직접 `close()`하는 방식은 지양한다.

좋지 않은 예:

```java
public String readFirstLineOrNull(String filePath) throws IOException {
    BufferedReader reader = new BufferedReader(new FileReader(filePath));
    String firstLine = reader.readLine();
    reader.close();
    return firstLine;
}
```

좋은 예:

```java
public String readFirstLineOrNull(String filePath) throws IOException {
    try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
        return reader.readLine();
    }
}
```

### 7.6. 삼항연산자 사용 규칙

- 삼항연산자(`?:`)는 단순한 값 선택에 사용한다.
- 분기 구조를 한눈에 파악하기 어려우면 `if/else`로 작성한다.
- 한 줄을 기본으로 하며, 두 결과의 비교에 도움이 될 때만 줄을 나눈다.
- 여러 줄로 작성할 때는 `?`와 `:`를 각각 다음 줄의 시작에 같은 들여쓰기로 배치한다.

좋지 않은 예:

```java
long shippingFeeInCents = isFreeShipping
    ? 0L
    : standardShippingFeeInCents;
```

좋은 예:

```java
long shippingFeeInCents = isFreeShipping ? 0L : standardShippingFeeInCents;
```

여러 줄로 두 결과를 비교하는 경우:

```java
ShippingQuote shippingQuote = isExpressDelivery
    ? calculateExpressShippingQuote(order, deliveryAddress)
    : calculateStandardShippingQuote(order, deliveryAddress);
```

## 8. `assert` 사용 규칙

- `assert`는 코드가 전제로 삼는 내부 불변식과 사전 조건을 확인할 때 사용한다.
- 외부 입력 검증이나 복구 가능한 오류 처리 수단으로 `assert`를 사용하지 않는다.
- `assert` 안의 표현식에 부수 효과를 넣지 않는다.
- 반드시 괄호로 감싸서 작성한다.

좋지 않은 예:

```java
assert denominator != 0;
```

좋은 예:

```java
assert (denominator != 0) : "denominator must not be zero";
assert (orderStatus != null) : "orderStatus must already be validated";
```

## 9. `null` 처리 규칙

- `null`은 원칙적으로 매개 변수와 반환값에 사용하지 않는다.
  - 호출자가 값을 전달하지 않는 상황을 허용해야 할 때만 예외적으로 매개 변수에 `null`을 허용한다.
  - 조회 실패나 생성 실패처럼 반환할 결과가 없는 상황을 호출자가 처리해야 할 때만 예외적으로 `null`을 반환한다.
- `null`을 허용하는 매개 변수 이름에는 `OrNull` 접미사를 붙인다.
- `null`을 반환하는 메서드 이름에는 `OrNull` 접미사를 붙인다.
- 컬렉션 반환값은 `null`이 아닌 빈 컬렉션을 반환한다.

좋지 않은 예:

```java
public User findUserByEmail(String emailOrNull);
public List<Order> findOrdersOrNull(UserId userId);
```

좋은 예:

```java
public User findUserByEmailOrNull(String emailOrNull);

public List<Order> findOrdersByUserId(UserId userId) {
    return mOrderRepository.findByUserId(userId);
}
```

## 10. `var` 사용 규칙

- `var` 키워드는 원칙적으로 사용하지 않는다.
- 자료형이 우변에서 명확히 드러나는 경우에는 사용할 수 있다.
- 향상된 for 문에서는 반복 대상의 요소 의미가 분명할 때 `var`를 사용할 수 있다.

좋지 않은 예:

```java
var currentUser = mUserService.findUserById(mCurrentUserId);
var orders = mOrderRepository.findAll();
```

좋은 예:

```java
User currentUser = mUserService.findUserById(mCurrentUserId);
List<Order> orders = mOrderRepository.findAll();
```

허용 가능한 예외:

```java
var newOrder = new Order();
var ordersById = new HashMap<OrderId, Order>();

for (var order : pendingOrders) {
    submitOrder(order);
}
```

## 11. 메서드 이름의 접미사 조합 규칙

- 접미사를 조합할 때는 해당하는 것만 `Recursive`, `OrNull` 순서로 붙인다.

좋지 않은 예:

```java
public Order findOrderOrNullRecursive(OrderNode rootNode, OrderId orderId);
```

좋은 예:

```java
public Order findOrderRecursiveOrNull(OrderNode rootNode, OrderId orderId);
```

## 12. 포맷팅 규칙

### 12.1. 들여쓰기 규칙

- 들여쓰기는 공백 4칸을 사용한다.

### 12.2. 중괄호 사용 규칙

- 여는 중괄호는 같은 줄에 둔다.
- 닫는 중괄호는 새로운 줄에 둔다.
- 단일 실행문을 포함한 모든 제어문에 중괄호를 사용한다.
- 실행할 동작이 없는 제어문 블록은 만들지 않는다.

좋지 않은 예:

```java
if (isValidRequest)
    proceed();
```

좋은 예:

```java
if (isValidRequest) {
    proceed();
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

### 12.3. 수정자 순서 규칙

- 접근 제어자는 다른 수정자보다 앞에 작성한다.
- `static`은 `final`보다 앞에 작성한다.

좋지 않은 예:

```java
static public void submitOrder(Order order) {
    order.submit();
}

private final static int RETRY_COUNT = 3;
```

좋은 예:

```java
public static void submitOrder(Order order) {
    order.submit();
}

private static final int RETRY_COUNT = 3;
```

### 12.4. 선언 규칙

- 한 줄에 변수 하나만 선언한다.
- 배열 선언은 자료형 쪽에 대괄호를 붙인다.
- 메서드 선언은 한 줄을 기본으로 작성하고 줄바꿈이 필요하면 12.6절의 기준을 따른다.

좋지 않은 예:

```java
int width = image.getWidth(), height = image.getHeight(), depth = image.getDepth();
String fileNames[] = fileLoader.getFileNames();
```

좋은 예:

```java
int width = image.getWidth();
int height = image.getHeight();
int depth = image.getDepth();
String[] fileNames = fileLoader.getFileNames();
```

### 12.5. 리터럴 표기 규칙

- `float` 리터럴에는 반드시 `f` 접미사를 붙인다.
- `long` 리터럴에는 반드시 대문자 `L` 접미사를 붙인다.
- 큰 숫자는 밑줄(`_`)로 자릿수를 구분할 수 있다.
- 매직 넘버를 직접 쓰지 말고 의미 있는 상수로 분리한다.
- 단위가 있는 숫자는 상수 이름에 단위를 드러낸다.

좋지 않은 예:

```java
float ratio = (float) 0.25;
long timeoutMillis = 1000l;
public static final long TIMEOUT = 3000L;
```

좋은 예:

```java
float ratio = 0.25f;
long timeoutMillis = 1_000L;
public static final long PAYMENT_TIMEOUT_MILLIS = 3_000L;
```

### 12.6. 줄바꿈과 빈 줄 규칙

- 줄바꿈은 글자 수가 아니라 의미와 구조의 가독성을 기준으로 결정한다.
- 단순한 표현식, 메서드 호출, 선언은 한 줄을 기본으로 작성한다.
- 조건식이나 계산식을 나눌 때는 의미 단위로 나눈다.
- 매개 변수와 인자 목록은 한눈에 파악하기 어려울 때만 나누고, 나누면 항목마다 한 줄씩 배치한다.
- 데이터 목록은 의미 있는 묶음이나 구조가 드러나도록 배치한다.
- 빈 줄은 논리적인 처리 단계를 구분할 때 사용한다.

좋지 않은 예:

```java
Order order = createOrder(customerId, items, shippingAddress);

submitOrder(order, paymentMethod, deliveryOptions,
    notificationOptions);
```

좋은 예:

```java
Order order = createOrder(customerId, items, shippingAddress);

submitOrder(order, paymentMethod, deliveryOptions, notificationOptions);
```

```java
Order order = createOrder(customerId, items, shippingAddress);

submitOrder(
    order,
    paymentMethod,
    deliveryOptions,
    notificationOptions);
```

## 13. 주석 작성 규칙

### 13.1. 주석 작성 원칙

- 주석은 코드가 무엇을 하는지 반복하지 않는다.
- 코드만으로 드러나지 않는 이유, 제약, 특별한 판단, 외부 시스템의 특성을 설명한다.
- 공개 API에는 호출자가 알아야 하는 계약이 있을 때만 문서 주석을 작성한다.
- 문서 주석에는 입력 제약, 반환값, 실패 시 동작 등 호출에 필요한 계약을 적는다.
- 임시 주석, 죽은 코드, 오래된 TODO는 남기지 않는다.
- TODO에는 담당자나 추적 가능한 이슈 번호를 함께 적는다.

좋지 않은 예:

```java
// Checks whether the payment attempt was submitted recently.
if (paymentAttempt.isRecentlySubmitted(mClock.instant())) {
    return PaymentResult.duplicateRequest();
}
```

좋은 예:

```java
// Payment gateway rejects duplicate approval requests within 30 seconds.
if (paymentAttempt.isRecentlySubmitted(mClock.instant())) {
    return PaymentResult.duplicateRequest();
}
```

TODO:

```java
// TODO(ORDER-482): Remove fallback after the legacy payment gateway is retired.
PaymentResult paymentResult = mPaymentGateway.approveOrFallback(paymentRequest);
```

### 13.2. 인라인 주석 규칙

- 주석은 줄 끝에 붙이지 않고 코드 위에 독립된 줄로 작성한다.

좋지 않은 예:

```java
final int PAYMENT_GATEWAY_DUPLICATE_STATUS_CODE = 409;

if (paymentResponse.getStatusCode() == PAYMENT_GATEWAY_DUPLICATE_STATUS_CODE) { // Payment gateway uses 409 for duplicate approval requests.
    return PaymentResult.duplicateRequest();
}
```

좋은 예:

```java
final int PAYMENT_GATEWAY_DUPLICATE_STATUS_CODE = 409;

// Payment gateway uses 409 for duplicate approval requests.
if (paymentResponse.getStatusCode() == PAYMENT_GATEWAY_DUPLICATE_STATUS_CODE) {
    return PaymentResult.duplicateRequest();
}
```

### 13.3. 주석보다 이름을 우선하는 규칙

- 변수, 메서드, 타입의 의미를 설명하기 위한 주석이 필요하다면 이름을 먼저 고친다.

좋지 않은 예:

```java
// Sends a password reset email.
send(user);
```

좋은 예:

```java
sendPasswordResetEmail(user);
```
