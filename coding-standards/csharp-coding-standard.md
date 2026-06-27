# C# 코딩 표준

## 1. 기본 원칙

이 코딩 표준은 가독성과 일관성을 최우선 가치로 삼는다.

코드는 단순히 동작하는 것을 넘어 읽는 사람이 빠르고 정확하게 의도를 이해할 수 있어야 한다.

- 가독성을 최우선으로 한다.
  - 코드는 주석 없이도 최대한 의도를 이해할 수 있어야 한다.
  - 복잡한 로직은 트릭보다 명확한 표현을 우선한다.
- 특별한 이유가 없는 한 IDE의 자동 서식 규칙을 따른다.
  - Visual Studio의 `Code Cleanup(코드 정리)` 기능을 기본으로 사용한다.
  - 개인 취향보다 팀 전체의 일관성을 우선한다.
- 이름은 역할과 의도를 드러내야 한다.
  - 축약어, 애매한 약어, 임의의 줄임말보다 명확한 이름을 사용한다.
  - 같은 개념에는 같은 이름을 사용한다.
- 자료형은 의미를 표현해야 한다.
  - 메서드의 매개 변수와 반환값은 가능한 한 강타입으로 설계한다.
  - 문자열, 숫자, 불리언으로 의미를 암묵적으로 전달하는 설계를 지양한다.
- 실패 조건은 가능한 한 빠르고 분명하게 처리한다.
  - 외부 입력은 시스템 경계에서 검증한다.
  - 내부 로직은 검증된 값과 도메인 자료형을 받도록 구성한다.
  - 내부 메서드의 사전 조건이 깨지면 즉시 실패가 드러나게 한다.

이 코딩 표준은 다음 자료를 참고하되 실제 개발 환경에서의 가독성과 유지보수성을 고려하여 일부 항목을 현실적으로 조정하였다.

- [.NET Runtime C# Coding Style](https://github.com/dotnet/runtime/blob/main/docs/coding-guidelines/coding-style.md)
- [Microsoft .NET Coding Conventions](https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions)

## 2. 파일, `using`, 네임스페이스, 클래스 구조

### 2.1. 파일 구성 순서

C# 소스 파일은 다음 순서로 구성한다.

1. 라이선스 또는 저작권 주석
2. `using` 지시문
3. 파일 범위 네임스페이스 선언
4. 클래스, 인터페이스, 구조체 또는 열거형 선언

각 영역 사이에는 빈 줄 하나를 둔다.

```cs
using System.Collections.Generic;

namespace OrderProcessing;

public class OrderService
{
}
```

### 2.2. `using` 지시문 규칙

- 사용하지 않는 `using` 지시문은 남기지 않는다.
- `using` 지시문은 IDE의 자동 정렬 규칙을 따른다.
- `global using`은 사용하지 않는다.
- `using static`은 테스트 코드나 가독성이 분명히 좋아지는 경우에만 제한적으로 사용한다.

### 2.3. 네임스페이스 규칙

- 네임스페이스 이름은 `PascalCase`를 사용한다.
- 파일 범위 네임스페이스 선언(C# 10.0)을 사용한다.

좋지 않은 예:

```cs
namespace OrderProcessing
{
    public class OrderApprovalService
    {
    }
}
```

좋은 예:

```cs
namespace OrderProcessing;

public class OrderApprovalService
{
}
```

### 2.4. 클래스와 파일 구조

- 클래스는 각각 독립된 소스 파일에 있어야 한다.
  - 단, 파일을 분리하는 것보다 함께 두는 편이 읽기 쉬운 작은 보조 클래스는 예외를 허용한다.
- 파일 이름은 대소문자까지 포함해서 반드시 클래스 이름과 일치해야 한다.

좋지 않은 예:

```cs
public class OrderApprovalService
{
}
```

```text
OrderService.cs
```

좋은 예:

```cs
public class OrderApprovalService
{
}
```

```text
OrderApprovalService.cs
```

### 2.5. `partial` 클래스 파일 이름 규칙

- 여러 파일이 하나의 클래스를 이룰 때 파일 이름은 클래스 이름으로 시작한다.
- 클래스 이름 뒤에는 마침표와 세부 항목 이름을 붙인다.
- 세부 항목 이름은 해당 파일이 맡는 책임을 드러내야 한다.

좋지 않은 예:

```text
OrderEditorViewModel1.cs
OrderEditorViewModelExtra.cs
OrderEditorViewModel_Commands.cs
```

좋은 예:

```cs
public partial class OrderEditorViewModel
{
}
```

```text
OrderEditorViewModel.Validation.cs
OrderEditorViewModel.Commands.cs
OrderEditorViewModel.Navigation.cs
```

## 3. 네이밍 규칙

### 3.1. 클래스, 인터페이스, 구조체, 열거형 이름 규칙

- 클래스는 `PascalCase`를 사용한다.
- 클래스 이름은 명사 또는 명사구로 작성한다.
- 인터페이스는 `PascalCase`를 사용하며 이름 앞에 `I` 접두어를 사용한다.
- 열거형은 `PascalCase`를 사용하며 이름 앞에 `E` 접두어를 사용한다.
- 구조체는 `PascalCase`를 사용하며 이름 앞에 `S` 접두어를 사용한다.
  - 단, `readonly struct`에는 `S` 접두어를 붙이지 않는다.
- 비트 플래그나 권한 조합처럼 플래그 성격의 열거형에는 `Flags` 접미사를 붙인다.
- 열거형 멤버는 `PascalCase`를 사용한다.

좋지 않은 예:

```cs
public interface PaymentGateway
{
}

public enum OrderStatus
{
    pending,
    paid,
}
```

좋은 예:

```cs
public class UserRegistrationService
{
}

public interface IPaymentGateway
{
}

public enum EOrderStatus
{
    Pending,
    Paid,
    ReadyToShip,
    Shipped,
    Canceled,
}

public struct SWindowSize
{
}

[Flags]
public enum EPermissionFlags
{
    None = 0,
    Read = 1 << 0,
    Write = 1 << 1,
    Delete = 1 << 2,
}
```

### 3.2. 메서드 이름 규칙

- 메서드 이름은 반드시 동사 또는 동사구로 시작한다.
- 가능하면 `동사 + 목적어` 형태로 작성한다.
- 반환값이 있는 메서드는 무엇을 반환하는지가 이름에 드러나야 한다.
- `bool` 값을 반환하는 메서드는 `Is`, `Has`, `Can`, `Should`를 우선 사용한다.
  - 이 표현이 부자연스러운 경우에는 상태를 나타내는 다른 3인칭 단수형 동사를 사용한다.
- 재귀 메서드는 이름 끝에 `Recursive`를 붙인다.
- `public` 메서드 이름은 `PascalCase`를 사용한다.
- `public` 메서드가 아닌 경우 `camelCase`를 사용한다.

좋지 않은 예:

```cs
public User UserById(UserId userId);
public void Process();
public bool Flag();
public decimal Calculate(OrderNode orderNode);
```

좋은 예:

```cs
public User FindUserById(UserId userId);
public IReadOnlyList<Order> FindPendingOrders(UserId userId);
public void ApprovePayment(PaymentId paymentId);
public bool IsExpired(Coupon coupon);
public bool HasPermission(User user, EPermissionFlags permission);
public bool CanCancel(Order order);
public bool ShouldRetry(PaymentAttempt paymentAttempt);
public decimal CalculateOrderTotalRecursive(OrderNode orderNode);

private User findUserById(UserId userId);
```

### 3.3. 변수 이름 규칙

- 지역 변수와 메서드의 매개 변수 이름은 `camelCase`를 사용한다.
- 변수 이름은 명사 또는 명사구를 사용한다.
- 값의 의미, 역할, 상태, 개수, 위치가 드러나야 한다.
- 의미 없는 축약어, 임의의 약어, 단일 문자 변수 이름은 사용하지 않는다.
  - 단, 짧은 반복문 인덱스처럼 관례적으로 명확한 경우는 예외로 한다.
- 단순 반복문 변수가 아닌 경우 `i`, `e` 같은 변수 이름 대신 `index`, `employee`처럼 변수에 담긴 값을 바로 알 수 있는 이름을 사용한다.
- 컬렉션은 복수형으로 작성한다.
- 키-값 대응 컬렉션은 가능하면 `xxxByYyy` 형태를 사용한다.
- `bool` 변수는 `is`, `has`, `can`, `should`를 우선 사용한다.

좋지 않은 예:

```cs
int cnt;
string str;
List<Order> list;
Dictionary<UserId, User> map;
bool flag;
```

좋은 예:

```cs
int retryCount;
int currentPage;
string invoiceFilePath;

List<Order> pendingOrders;
Dictionary<OrderId, Order> ordersById;

bool isPaymentApproved;
bool hasDeliveryAddress;
bool canRetry;
bool shouldUpdateCache;
```

### 3.4. 멤버 변수 이름 규칙

- `private` 인스턴스 멤버 변수는 앞에 `m`을 붙이고 `PascalCase`를 사용한다.
- `static` 멤버 변수는 앞에 `s`를 붙이고 `PascalCase`를 사용한다.
- 접두어 뒤에는 멤버 변수의 원래 이름을 대문자로 시작하여 이어 붙인다.
- `bool` 멤버 변수는 `mIs`, `mHas`, `mCan`, `mShould` 형태를 우선 사용한다.
- 상수와 상수 역할을 하는 `static readonly` 값은 `s` 접두어를 사용하지 않고 3.6의 상수 이름 규칙을 따른다.

좋은 예:

```cs
public class UserProfile
{
    private int mRetryCount;
    private bool mIsEnabled;
    private bool mHasPermission;

    private static string sCurrentTimeZone = "Asia/Seoul";
}
```

### 3.5. 프로퍼티 이름 규칙

- 프로퍼티 이름은 `PascalCase`를 사용한다.
- `bool` 프로퍼티는 앞에 `Is`, `Has`, `Can`, `Should` 중 하나를 붙인다.

좋은 예:

```cs
public bool IsClosed { get; private set; }
public bool HasUnreadMessage { get; private set; }
public bool CanExport { get; private set; }
public bool ShouldRefresh { get; private set; }
```

### 3.6. 상수 이름 규칙

- 상수의 이름은 모두 대문자와 밑줄을 사용한다.
- 객체 값을 상수처럼 사용할 때는 `static readonly`를 사용한다.
- 상수로 사용하는 `static readonly` 멤버 변수는 모두 대문자와 밑줄을 사용한다.

좋지 않은 예:

```cs
const int MaxRetryCount = 3;
```

좋은 예:

```cs
const int MAX_RETRY_COUNT = 3;
const string DEFAULT_COUNTRY_CODE = "KR";

public static readonly CurrencyInfo DEFAULT_CURRENCY = new CurrencyInfo("KRW");
```

## 4. 멤버 구성 및 순서

클래스 내부 구성은 다음 순서를 따른다.

1. 멤버 변수
2. 프로퍼티
   - 단, 프로퍼티와 대응하는 `private` 멤버 변수는 프로퍼티 바로 위에 적는다.
3. 생성자
4. 메서드
   - `public` -> `private` 순서로 배치한다.

추가 원칙은 다음과 같다.

- 공개 메서드가 먼저 보이도록 배치한다.
- 필요에 따라 관련된 멤버끼리 논리적으로 묶어 배치할 수 있다.
- `private` 헬퍼 메서드는 이를 사용하는 `public` 메서드 아래쪽에 배치한다.
- 오버로드된 메서드는 서로 떨어뜨리지 않고 함께 배치한다.
- 멤버 변수는 연관 있는 것끼리 그룹으로 묶는다.

좋은 예:

```cs
public class OrderService
{
    private static string sCurrentCurrencyCode = "KRW";

    private readonly IOrderRepository mOrderRepository;
    private readonly IPaymentGateway mPaymentGateway;
    private bool mHasPendingRequest;

    public int OrderCount
    {
        get;
        private set;
    }

    public OrderService(IOrderRepository orderRepository, IPaymentGateway paymentGateway)
    {
        mOrderRepository = orderRepository;
        mPaymentGateway = paymentGateway;
    }

    public Receipt ApproveOrder(OrderId orderId)
    {
        Order? order = findOrderOrNull(orderId);
        if (order == null || order.CanApprovePayment() == false)
        {
            return Receipt.Rejected(orderId);
        }

        mPaymentGateway.Approve(order.PaymentId);
        order.MarkAsPaid();
        mOrderRepository.Save(order);
        return Receipt.Approved(orderId);
    }

    private Order? findOrderOrNull(OrderId orderId)
    {
        return mOrderRepository.FindByIdOrNull(orderId);
    }
}
```

## 5. 접근 제어 및 캡슐화

### 5.1. 접근 제한자 규칙

- 멤버에는 가능한 한 가장 좁은 접근 수준을 사용한다.
- 외부에서 사용해야 하는 API만 `public`으로 공개한다.
- 같은 어셈블리 내부 구현에만 필요한 타입과 멤버는 `internal` 사용을 검토한다.
- 외부에서 직접 변경하면 안 되는 상태는 `private` 멤버 변수와 제한된 setter로 보호한다.
- `public` 멤버 변수는 사용하지 않는다.

좋지 않은 예:

```cs
public class OrderSummary
{
    public int CompletedOrderCount;
}
```

좋은 예:

```cs
public class OrderSummary
{
    public int CompletedOrderCount { get; private set; }

    public void AddCompletedOrder()
    {
        ++CompletedOrderCount;
    }
}
```

### 5.2. 프로퍼티 사용 규칙

- getter와 setter 대신 프로퍼티를 사용한다.
- 외부에 공개해야 하는 상태는 메서드보다 프로퍼티로 표현한다.
- 단순 값 변경보다 의미 있는 상태 변경을 표현하는 메서드를 우선한다.

좋지 않은 예:

```cs
public class UserProfile
{
    private string mDisplayName;

    public string GetDisplayName()
    {
        return mDisplayName;
    }

    public void SetDisplayName(string displayName)
    {
        mDisplayName = displayName;
    }
}
```

좋은 예:

```cs
public class UserProfile
{
    public string DisplayName { get; private set; }

    public UserProfile(string displayName)
    {
        DisplayName = displayName;
    }
}
```

의미 있는 상태 변경을 표현하는 메서드 예:

```cs
public class UserProfile
{
    public bool HasMarketingConsent { get; private set; }

    public void GrantMarketingConsent()
    {
        HasMarketingConsent = true;
    }
}
```

### 5.3. 읽기 전용 값 규칙

- 초기화 후 값이 변하지 않는 변수는 `readonly`로 선언한다.
- 프로퍼티에 `private init`(C# 9.0)을 가능한 한 사용한다.
- 객체 초기자(object initializer)는 지양한다.
  - 단, `required` 한정자(C# 11.0)와 초기화 전용 setter(C# 9.0)로 불변 객체를 구성할 때는 허용한다.

좋은 예:

```cs
public class Account
{
    private readonly PasswordHash mPasswordHash;
    private readonly IUserRepository mUserRepository;

    public EmailAddress EmailAddress { get; private init; }

    public Account(PasswordHash passwordHash, EmailAddress emailAddress, IUserRepository userRepository)
    {
        mPasswordHash = passwordHash;
        EmailAddress = emailAddress;
        mUserRepository = userRepository;
    }
}
```

### 5.4. 싱글턴과 `static` 클래스 규칙

- 싱글턴 패턴 대신 정적(`static`) 클래스를 사용한다.
- 상태를 보관해야 하거나 의존성이 필요한 경우에는 `static` 클래스보다 일반 클래스를 사용한다.

좋은 예:

```cs
public static class DateTimeUtility
{
    public static DateTime GetStartOfDay(DateTime dateTime)
    {
        return new DateTime(dateTime.Year, dateTime.Month, dateTime.Day);
    }
}
```

## 6. 변수 및 메서드 설계 규칙

### 6.1. 지역 변수 선언 규칙

- 지역 변수는 필요한 시점에 선언하고 선언과 동시에 의미 있는 값으로 초기화한다.
- 같은 범위 안에서는 지역 변수마다 서로 다른 역할이 이름에 분명히 드러나야 한다.

좋지 않은 예:

```cs
decimal amount;

if (order.HasDiscountCoupon())
{
    amount = CalculateDiscountAmount(order);
    order.ApplyDiscount(amount);
}
```

좋은 예:

```cs
if (order.HasDiscountCoupon())
{
    decimal discountAmount = CalculateDiscountAmount(order);
    order.ApplyDiscount(discountAmount);
}
```

### 6.2. 긴 표현식 분리 규칙

- 인덱스, 오프셋, 크기, 좌표 계산처럼 여러 의미가 섞인 계산을 하나의 표현식에 몰아넣지 않는다.
- 표현식이 길어지면 의미 있는 계산 단위로 나누어 지역 변수에 담는다.
- 중간 변수 이름은 계산 방식보다 값의 의미를 드러내야 한다.
- 단순히 줄 길이를 줄이기 위한 `temp`, `value`, `result` 같은 이름은 사용하지 않는다.
- 한눈에 이해되는 단순 표현식은 불필요하게 나누지 않는다.

좋지 않은 예:

```cs
int sourceByteIndex = (region.Y + y) * bytesPerRow + (region.X + x) * channelCount + channelIndex;
```

좋은 예:

```cs
int sourceRowOffset = (region.Y + y) * bytesPerRow;
int sourceColumnOffset = (region.X + x) * channelCount;

int sourceByteIndex = sourceRowOffset + sourceColumnOffset + channelIndex;
```

### 6.3. 메서드 오버로딩 규칙

- 매개 변수 자료형만 다른 모호한 오버로딩은 지양한다.
- 이름만으로도 동작 차이가 드러나야 한다.
- 같은 자료형을 매개 변수로 받지만 의미가 다른 메서드는 이름을 분리한다.

좋지 않은 예:

```cs
public User Search(UserId userId);
public IReadOnlyList<User> Search(SearchKeyword searchKeyword);
```

좋은 예:

```cs
public User FindUserById(UserId userId);
public IReadOnlyList<User> SearchUsersByKeyword(SearchKeyword searchKeyword);
```

### 6.4. 기본 매개 변수 규칙

- 기본 매개 변수보다 명시적인 메서드 오버로딩을 우선 사용한다.
- 기본 매개 변수를 사용하는 경우 기본값의 의미가 분명한 `null`, `false`, `0` 같은 값을 사용한다.

좋은 예:

```cs
public enum EOrderDeliveryMethod
{
    Email,
    PostalMail,
}

public void SendOrder(Order order)
{
    SendOrder(order, EOrderDeliveryMethod.Email);
}

public void SendOrder(Order order, EOrderDeliveryMethod deliveryMethod)
{
    mOrderSender.Send(order, deliveryMethod);
}
```

허용 가능한 예:

```cs
public IReadOnlyList<Order> SearchOrders(SearchKeyword searchKeyword, int pageNumber = 0)
{
    return mOrderRepository.Search(searchKeyword, pageNumber);
}
```

### 6.5. 변수 가리기 금지

- 변수 가리기(variable shadowing)는 허용하지 않는다.
- 외부 변수가 동일한 이름을 사용 중이라면 내부 변수에는 다른 이름을 사용한다.

좋지 않은 예:

```cs
public class OrderService
{
    private int mRetryCount;

    public void SubmitOrders(int mRetryCount)
    {
        if (mRetryCount <= 0)
        {
            return;
        }

        SubmitNextOrder(mRetryCount);
    }
}
```

좋은 예:

```cs
public class OrderService
{
    private int mRetryCount;

    public void SubmitOrders(int maxRetryCount)
    {
        int orderCount = mOrderRepository.CountPendingOrders();

        for (int i = 0; i < orderCount; ++i)
        {
            SubmitNextOrder(maxRetryCount);
        }
    }
}
```

### 6.6. `out` 매개 변수 선언 규칙

- 메서드에 전달하는 `out` 매개 변수는 별도의 줄에서 먼저 선언한다.
- 인수 목록 안에서 `out` 변수를 선언하지 않는다.
- `out` 변수 이름은 반환되는 값의 의미를 드러내야 한다.

좋지 않은 예:

```cs
bool isParsed = int.TryParse(text, out int value);
```

좋은 예:

```cs
int parsedValue;
bool isParsed = int.TryParse(text, out parsedValue);
```

### 6.7. 강타입 매개 변수 설계 규칙

- 매개 변수는 가능한 한 강타입으로 받는다.
- 의미를 암묵적으로 담은 `string`, `int`, `long`, `bool`의 직접 사용을 지양한다.
- 정해진 선택지는 `enum`으로 표현한다.
- 도메인 의미가 있는 값은 전용 자료형으로 감싸는 것을 우선 검토한다.
- 전용 자료형은 가능한 한 불변으로 설계한다.
- 범용적인 값을 강타입으로 만들 때는 `readonly record struct`(C# 10.0)를 우선 검토한다.
- 관련된 여러 매개 변수는 별도의 객체로 묶는 것을 우선 검토한다.
- 의미가 다른 동일 자료형 매개 변수를 나란히 받는 설계를 피한다.
- `bool` 매개 변수는 호출부 가독성을 떨어뜨리므로 가능한 한 사용하지 않는다.

#### 열거형 사용 예

```cs
public enum ESortOrder
{
    Ascending,
    Descending,
}

public IReadOnlyList<User> SortUsers(IReadOnlyList<User> users, ESortOrder sortOrder)
{
    return mUserRepository.SortByOrder(users, sortOrder);
}
```

#### 전용 자료형 사용 예

```cs
public readonly record struct UserId(long Value);
public readonly record struct SearchKeyword(string Value);

public User FindUserById(UserId userId)
{
    return mUserRepository.FindById(userId);
}

public IReadOnlyList<User> SearchUsers(SearchKeyword searchKeyword)
{
    return mUserRepository.Search(searchKeyword);
}
```

#### 매개 변수 객체 사용 예

```cs
public sealed class OrderSearchCriteria
{
    public required SearchKeyword Keyword { get; init; }
    public required EOrderStatus Status { get; init; }
    public required int PageNumber { get; init; }
    public required int PageSize { get; init; }
}

public IReadOnlyList<Order> SearchOrders(OrderSearchCriteria criteria)
{
    return mOrderRepository.Search(criteria);
}
```

#### `bool` 매개 변수 지양 예

좋지 않은 예:

```cs
public void SaveOrder(Order order, bool shouldOverwrite);
```

좋은 예:

```cs
public enum ESaveMode
{
    CreateNew,
    Overwrite,
}

public void SaveOrder(Order order, ESaveMode saveMode);
```

### 6.8. 컬렉션 자료형 사용 규칙

- `System.Collections`의 비제네릭 컬렉션보다 `System.Collections.Generic`의 제네릭 컬렉션을 사용한다.
- 일반 배열을 사용하는 것도 허용한다.

좋은 예:

```cs
List<Order> completedOrders = new List<Order>();
Dictionary<UserId, User> usersById = new Dictionary<UserId, User>();
string[] fileNames = new string[10];
```

### 6.9. 입력 검증 규칙

- 외부 입력은 시스템 경계에서 검증한다.
- 검증 실패는 시스템 경계에서 즉시 처리한다.
- 내부 메서드는 이미 검증된 값을 받는다고 가정한다.
- 내부 메서드의 사전 조건이 깨지면 즉시 실패가 드러나게 한다.

좋은 예:

```cs
private readonly IUserRepository mUserRepository;

public User? FindUserByEmailOrNull(string? emailOrNull)
{
    if (string.IsNullOrWhiteSpace(emailOrNull))
    {
        return null;
    }

    EmailAddress emailAddress = new EmailAddress(emailOrNull);
    return findUserByEmailInternal(emailAddress);
}

private User findUserByEmailInternal(EmailAddress emailAddress)
{
    Debug.Assert(string.IsNullOrWhiteSpace(emailAddress.Value) == false);
    return mUserRepository.FindByEmail(emailAddress);
}
```

## 7. 제어문 규칙

### 7.1. 빠른 반환 규칙

- 실패 조건이나 중단 조건은 앞에서 처리한다.
- 정상 흐름이 깊은 중첩 안에 들어가지 않도록 한다.
- 빠른 반환을 사용할 때도 반환값의 의미가 분명해야 한다.

좋은 예:

```cs
public Receipt ApprovePayment(Order order)
{
    if (order.CanApprovePayment() == false)
    {
        return Receipt.Rejected(order.Id);
    }

    Payment payment = mPaymentGateway.Approve(order.PaymentId);
    return Receipt.Approved(order.Id, payment.ApprovedAt);
}
```

### 7.2. `switch` 문 규칙

- `switch` 문에는 언제나 `default:` 케이스를 넣는다.
- `switch` 문에서 `default:` 케이스가 절대 실행될 일이 없는 경우 `default:` 안에 `Debug.Fail()`을 추가한다.

좋지 않은 예:

```cs
switch (orderStatus)
{
    case EOrderStatus.Paid:
        PrepareShipping();
        break;
    case EOrderStatus.ReadyToShip:
        CreateShipment();
        break;
}
```

좋은 예:

```cs
switch (orderStatus)
{
    case EOrderStatus.Pending:
        RequestPayment();
        break;
    case EOrderStatus.Paid:
        PrepareShipping();
        break;
    case EOrderStatus.ReadyToShip:
        CreateShipment();
        break;
    case EOrderStatus.Shipped:
        NotifyShipmentCompleted();
        break;
    case EOrderStatus.Canceled:
        CloseOrder();
        break;
    default:
        break;
}
```

도달할 수 없다고 가정하는 경우:

```cs
switch (orderStatus)
{
    case EOrderStatus.Pending:
        RequestPayment();
        break;
    case EOrderStatus.Paid:
        PrepareShipping();
        break;
    case EOrderStatus.ReadyToShip:
        CreateShipment();
        break;
    case EOrderStatus.Shipped:
        NotifyShipmentCompleted();
        break;
    case EOrderStatus.Canceled:
        CloseOrder();
        break;
    default:
        Debug.Fail("Unexpected order status: " + orderStatus);
        break;
}
```

### 7.3. 반복문 규칙

- 컬렉션 전체를 순회할 때는 `foreach` 문을 우선한다.
- 인덱스가 필요한 경우에만 `for` 문을 사용한다.
- 반복 중 컬렉션을 수정해야 한다면 안전한 수정 방식이 분명해야 한다.

좋은 예:

```cs
foreach (Order order in orders)
{
    SubmitOrder(order);
}

for (int index = 0; index < orders.Count; ++index)
{
    UpdateOrderPosition(orders[index], index);
}
```

### 7.4. 람다 사용 규칙

- 인라인 람다는 한 줄짜리 짧은 코드로 제한한다.
- 여러 작업을 수행하는 람다는 메서드나 명시적인 반복문으로 분리한다.

좋지 않은 예:

```cs
orders.ForEach(order =>
{
    ReserveInventory(order);
    SendOrderConfirmationEmail(order);
});
```

좋은 예:

```cs
foreach (Order order in orders)
{
    ReserveInventory(order);
    SendOrderConfirmationEmail(order);
}
```

### 7.5. 리소스 해제 규칙

- `using` 선언(C# 8.0)은 사용하지 않는다.
- 리소스의 수명 범위가 코드 블록에 드러나도록 `using` 문을 사용한다.

좋지 않은 예:

```cs
using StreamReader reader = new StreamReader(filePath);
string fileContent = reader.ReadToEnd();
```

좋은 예:

```cs
using (StreamReader reader = new StreamReader(filePath))
{
    string fileContent = reader.ReadToEnd();
}
```

## 8. `Debug.Assert()` 사용 규칙

- `Debug.Assert()`는 코드가 전제로 삼는 내부 불변식과 사전 조건을 확인할 때 사용한다.
- 특정 조건이 반드시 충족되어야 한다고 가정한 지점에는 `Debug.Assert()`를 둔다.
- `Debug.Assert()`는 외부 입력 검증이나 복구 가능한 오류 처리 수단으로 사용하지 않는다.

좋은 예:

```cs
public decimal CalculateAverageUnitPrice(Order order)
{
    Debug.Assert(order.ItemCount > 0);

    return order.TotalAmount / order.ItemCount;
}
```

## 9. `null` 처리 규칙

- `null`은 원칙적으로 매개 변수와 반환값에 사용하지 않는다.
  - 호출자가 값을 전달하지 않는 상황을 허용해야 할 때만 예외적으로 매개 변수에 `null`을 허용한다.
  - 조회 실패나 생성 실패처럼 반환할 결과가 없는 상황을 호출자가 처리해야 할 때만 예외적으로 `null`을 반환한다.
- `null`을 허용하는 매개 변수 이름에는 `OrNull`을 붙인다.
- `null`을 반환하는 메서드 이름에는 `OrNull`을 붙인다.
- 컬렉션 반환값은 `null`이 아닌 빈 컬렉션을 반환한다.
- `null` 병합 연산자(C# 2.0)는 사용하지 않는다.

좋지 않은 예:

```cs
public User? FindUserByEmail(string? emailOrNull);
public IReadOnlyList<Order>? FindOrdersOrNull(UserId userId);
```

좋은 예:

```cs
public User? FindUserByEmailOrNull(string? emailOrNull)
{
    if (string.IsNullOrWhiteSpace(emailOrNull))
    {
        return null;
    }

    return mUserRepository.FindByEmailOrNull(emailOrNull);
}

public IReadOnlyList<Order> FindOrdersByUserId(UserId userId)
{
    return mOrderRepository.FindByUserId(userId);
}
```

## 10. `var` 사용 규칙

- `var` 키워드는 원칙적으로 사용하지 않는다.
- 자료형이 우변에서 명확히 드러나는 경우에는 사용할 수 있다.
- LINQ 결과처럼 명시할 자료형이 지나치게 길거나 익명 타입처럼 자료형을 직접 적을 수 없는 경우에도 `var`를 사용할 수 있다.
- `foreach` 문에서는 반복 대상의 요소 의미가 분명할 때 `var`를 사용할 수 있다.
- `new` 키워드 뒤에는 반드시 명시적으로 자료형을 적는다.
  - 즉, C# 9.0의 `new()` 사용을 금지한다.
  - 명시할 자료형이 없는 익명 타입은 메서드 내부에서만 예외적으로 허용한다.

좋지 않은 예:

```cs
var currentUser = mUserService.FindUserById(mCurrentUserId);
var orders = mOrderRepository.FindAll();

User user = new();
```

좋은 예:

```cs
User currentUser = mUserService.FindUserById(mCurrentUserId);
IReadOnlyList<Order> orders = mOrderRepository.FindAll();
Dictionary<OrderId, Order> ordersById = new Dictionary<OrderId, Order>();

User newUser = new User();
```

허용 가능한 예외:

```cs
var newOrder = new Order();
var ordersById = new Dictionary<OrderId, Order>();

var ordersByCustomerId = pendingOrders.GroupBy(order => order.CustomerId);

foreach (var order in pendingOrders)
{
    SubmitOrder(order);
}
```

## 11. 비동기 처리 규칙

- 비동기 메서드 이름에는 `Async` 접미사를 붙인다.
- 비동기 메서드는 `Task` 또는 `Task<T>`를 반환한다.
- 성능상 필요한 경우에만 `ValueTask` 또는 `ValueTask<T>`를 사용할 수 있다.
- `async void`는 사용하지 않는다.

좋지 않은 예:

```cs
public async void SaveOrder(Order order, CancellationToken cancellationToken)
{
    await mOrderRepository.SaveAsync(order, cancellationToken);
}

public async Task SubmitOrder(Order order, CancellationToken cancellationToken)
{
    await mOrderRepository.SubmitAsync(order, cancellationToken);
}
```

좋은 예:

```cs
public async Task SaveOrderAsync(Order order, CancellationToken cancellationToken)
{
    await mOrderRepository.SaveAsync(order, cancellationToken);
}

public async Task<Receipt> SubmitOrderAsync(Order order, CancellationToken cancellationToken)
{
    Payment payment = await mPaymentGateway.ApproveAsync(order.PaymentId, cancellationToken);
    return Receipt.Approved(order.Id, payment.ApprovedAt);
}
```

## 12. 포맷팅 규칙

### 12.1. 들여쓰기 규칙

- 탭(tab)은 Visual Studio 기본값을 사용한다.
- Visual Studio를 사용하지 않을 때는 공백 4칸을 들여쓰기 기준으로 사용한다.

### 12.2. 중괄호 사용 규칙

- 중괄호(`{`)를 열 때는 언제나 새로운 줄에 연다.
- 단일 실행문이어도 중괄호를 생략하지 않는다.

좋지 않은 예:

```cs
if (shouldCloseWindow) return;
```

좋은 예:

```cs
if (shouldCloseWindow)
{
    return;
}
```

### 12.3. 선언 규칙

- 한 줄에 변수 하나만 선언한다.
- 메서드 선언이 한 줄에 읽기 어려울 만큼 길면 매개 변수를 한 줄에 하나씩 나눈다.

좋지 않은 예:

```cs
int width = image.Width, height = image.Height;
```

좋은 예:

```cs
int width = image.Width;
int height = image.Height;
```

### 12.4. 리터럴 표기 규칙

- `float` 리터럴에는 `f` 접미사를 붙인다.
- `decimal` 리터럴에는 `m` 접미사를 붙인다.
- `long` 리터럴 접미사가 필요한 경우 대문자 `L`을 사용한다.
- 큰 숫자는 밑줄(`_`)로 자릿수를 구분할 수 있다.
- 매직 넘버를 직접 쓰지 말고 의미 있는 상수로 분리한다.
- 단위가 있는 숫자는 상수 이름에 단위를 드러낸다.

좋지 않은 예:

```cs
float taxRate = (float)0.1;
decimal minimumOrderAmountWon = 19900M;
long paymentTimeoutMilliseconds = 3000l;
```

좋은 예:

```cs
const float DEFAULT_TAX_RATE = 0.1f;
const decimal MINIMUM_ORDER_AMOUNT_WON = 19_900m;
const long PAYMENT_TIMEOUT_MILLISECONDS = 3_000L;
```

## 13. 주석 작성 규칙

### 13.1. 주석 작성 원칙

- 주석은 코드가 무엇을 하는지 반복하지 않는다.
- 코드만으로 드러나지 않는 이유, 제약, 특별한 판단, 외부 시스템의 특성을 설명한다.
- 공개 API에는 호출자가 알아야 하는 계약이 있을 때만 문서 주석을 작성한다.
- 문서 주석에는 매개 변수 제약, `null` 허용 여부, 반환값, 실패 시 동작처럼 호출자가 알아야 하는 내용을 적는다.
- 임시 주석, 죽은 코드, 오래된 TODO는 남기지 않는다.
- TODO에는 담당자나 추적 가능한 이슈 번호를 함께 적는다.

좋지 않은 예:

```cs
// Checks whether the payment attempt was submitted recently.
if (paymentAttempt.IsRecentlySubmitted(mClock.GetCurrentInstant()))
{
    return PaymentResult.DuplicateRequest();
}
```

좋은 예:

```cs
// Payment gateway rejects duplicate approval requests within 30 seconds.
if (paymentAttempt.IsRecentlySubmitted(mClock.GetCurrentInstant()))
{
    return PaymentResult.DuplicateRequest();
}
```

TODO를 남겨야 하는 경우:

```cs
// TODO(ORDER-482): Remove fallback after the legacy payment gateway is retired.
PaymentResult paymentResult = mPaymentGateway.ApproveOrFallback(paymentRequest);
```

### 13.2. 인라인 주석 규칙

- 인라인 주석은 줄 끝에 길게 붙이지 않는다.
- 코드 위에 독립된 줄로 작성한다.

좋지 않은 예:

```cs
if (paymentResponse.StatusCode == 409) // Payment gateway uses 409 for duplicate approval requests.
{
    return PaymentResult.DuplicateRequest();
}
```

좋은 예:

```cs
// Payment gateway uses 409 for duplicate approval requests.
if (paymentResponse.StatusCode == 409)
{
    return PaymentResult.DuplicateRequest();
}
```

### 13.3. 주석보다 이름을 우선하는 규칙

- 변수, 메서드, 타입의 의미를 설명하기 위한 주석이 필요하다면 이름을 먼저 고친다.

좋지 않은 예:

```cs
// Sends a password reset email.
Send(user);
```

좋은 예:

```cs
SendPasswordResetEmail(user);
```
