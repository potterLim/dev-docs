# C# 코딩 표준

## 1. 기본 원칙

이 코딩 표준은 가독성과 일관성을 최우선 가치로 삼는다.  
코드는 단순히 동작하는 것을 넘어, 읽는 사람이 빠르고 정확하게 의도를 이해할 수 있어야 한다.

- 가독성을 최우선으로 한다.
  - 코드는 주석 없이도 최대한 의도를 이해할 수 있어야 한다.
  - 복잡한 로직은 트릭보다 명확한 표현을 우선한다.
- 특별한 이유가 없는 한 IDE의 자동 서식 규칙을 따른다.
  - Visual Studio의 `Format Document(문서 서식 지정)` 기능을 기본으로 사용한다.
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

이 코딩 표준은 다음 코딩 표준들을 참고하되, 실제 개발 환경에서의 가독성과 유지보수성을 고려하여 일부 항목을 현실적으로 조정하였다.

- [언리얼 엔진 4 코딩 표준](https://docs.unrealengine.com/latest/INT/Programming/Development/CodingStandard/)
- [둠 3 코드 스타일과 규칙](ftp://ftp.idsoftware.com/idstuff/doom3/source/codestyleconventions.doc)
- [IDesign C# 코딩 표준](http://www.idesign.net/downloads/getdownload/1985)

## 2. 파일, 네임스페이스, 클래스 구조

### 2.1. 네임스페이스 규칙

- 네임스페이스 이름은 PascalCase를 사용한다.
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

### 2.2. 클래스와 파일 구조

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

### 2.3. partial 클래스 파일 이름 규칙

- 여러 파일이 하나의 클래스를 이룰 때, 파일 이름은 클래스 이름으로 시작한다.
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

- 클래스와 구조체는 PascalCase를 사용한다.
- 인터페이스는 PascalCase를 사용하며, 이름 앞에 `I` 접두어를 사용한다.
- 열거형은 PascalCase를 사용하며, 이름 앞에 `E` 접두어를 사용한다.
- 구조체는 PascalCase를 사용하며, 이름 앞에 `S` 접두어를 사용한다.
  - 단, `readonly struct`일 때는 그렇지 않다.
- 비트 플래그나 권한 조합처럼 플래그 성격의 열거형에는 `Flags` 접미사를 붙인다.

좋지 않은 예:

```cs
public interface PaymentGateway
{
}

public enum PaymentStatus
{
    pending,
    approved,
}
```

좋은 예:

```cs
public class PaymentApprovalService
{
}

public interface IPaymentGateway
{
}

public enum EPaymentStatus
{
    Pending,
    Approved,
    Declined,
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
  - 그러는 것이 부자연스러운 경우에는 상태를 나타내는 다른 3인칭 단수형 동사를 사용한다.
- 재귀 메서드는 이름 끝에 `Recursive`를 붙인다.
- public 메서드 이름은 PascalCase를 사용한다.
- public 메서드가 아닌 경우 camelCase를 사용한다.

좋지 않은 예:

```cs
public Invoice Invoice(InvoiceID invoiceID);
public void Process();
public bool Flag();
public decimal Calculate(OrderNode orderNode);
```

좋은 예:

```cs
public Invoice GetInvoiceByID(InvoiceID invoiceID);
public IReadOnlyList<Order> GetPendingOrders(CustomerID customerID);
public bool ApprovePayment(PaymentID paymentID);
public bool IsExpired(Coupon coupon);
public bool HasPermission(User user, EPermissionFlags permission);
public bool CanCancel(Order order);
public bool ShouldRetry(PaymentAttempt paymentAttempt);
public decimal CalculateOrderTotalRecursive(OrderNode orderNode);

private Invoice getInvoiceByID(InvoiceID invoiceID);
```

### 3.3. 변수 이름 규칙

- 지역 변수와 메서드의 매개 변수 이름은 camelCase를 사용한다.
- 변수 이름은 명사 또는 명사구를 사용한다.
- 값의 의미, 역할, 상태, 개수, 위치가 드러나야 한다.
- 의미 없는 축약어, 임의의 약어, 단일 문자 변수 이름은 사용하지 않는다.
  - 단, 짧은 반복문 인덱스처럼 관례적으로 명확한 경우는 예외로 한다.
- 단순 반복문 변수가 아닌 경우 `i`, `e` 같은 변수 이름 대신 `index`, `employee`처럼 저장된 데이터를 바로 알 수 있는 이름을 사용한다.
- 컬렉션은 복수형으로 작성한다.
- 키-값 대응 컬렉션은 가능하면 `xxxByYyy` 형태를 사용한다.
- `bool` 변수는 `is`, `has`, `can`, `should`를 우선 사용한다.

좋지 않은 예:

```cs
int cnt;
string str;
List<Order> list;
Dictionary<UserID, User> map;
bool flag;
```

좋은 예:

```cs
public void SendInvoiceEmail(InvoiceID invoiceID, EmailAddress recipientEmailAddress)
{
    int retryCount;
    int currentPage;
    string invoiceFilePath;

    List<Order> pendingOrders;
    Dictionary<UserID, User> usersByID;

    bool isPaymentApproved;
    bool hasDeliveryAddress;
    bool canRetry;
    bool shouldUpdateCache;
}
```

### 3.4. 멤버 변수 이름 규칙

- `private` 인스턴스 멤버 변수는 앞에 `m`을 붙이고 PascalCase를 사용한다.
- `static` 멤버 변수는 앞에 `s`를 붙이고 PascalCase를 사용한다.
- 접두어 뒤에는 멤버 변수의 원래 이름을 대문자로 시작하여 이어 붙인다.
- `bool` 멤버 변수는 `mIs`, `mHas`, `mCan`, `mShould` 형태를 우선 사용한다.
- 상수와 상수 역할을 하는 `static readonly` 값은 `s` 접두어를 사용하지 않고, 3.6의 상수 이름 규칙을 따른다.

좋은 예:

```cs
public class PaymentProcessor
{
    private int mRetryCount;
    private string mGatewayName = string.Empty;
    private bool mIsConnected;
    private bool mHasPendingRequest;

    private static string sDefaultCurrencyCode = "KRW";
    private static int sMaxRetryCount;
}
```

### 3.5. 프로퍼티 이름 규칙

- 프로퍼티 이름은 PascalCase를 사용한다.
- `bool` 프로퍼티는 앞에 `Is`, `Has`, `Can`, `Should` 중 하나를 붙인다.

좋은 예:

```cs
public bool IsClosed { get; private set; }
public bool HasUnreadMessage { get; private set; }
public bool CanExport { get; private set; }
public bool ShouldRefresh { get; private set; }
```

### 3.6. 상수와 줄임말 규칙

- 상수의 이름은 모두 대문자와 밑줄을 사용한다.
- 객체 값을 상수처럼 사용할 때는 `static readonly`를 사용한다.
- 상수로 사용하는 `static readonly` 멤버 변수는 모두 대문자와 밑줄을 사용한다.
- 뒤에 추가적인 단어가 오지 않는 경우 줄임말은 모두 대문자로 표기한다.
- 줄임말 뒤에 다른 단어가 이어지면 일반 단어처럼 PascalCase를 사용한다.

좋지 않은 예:

```cs
const int MaxRetryCount = 3;
public int OrderId { get; private set; }
public int HTTPCode { get; private set; }
```

좋은 예:

```cs
const int MAX_RETRY_COUNT = 3;
const string DEFAULT_COUNTRY_CODE = "KR";

public static readonly CurrencyInfo DEFAULT_CURRENCY = new CurrencyInfo("KRW");

public int OrderID { get; private set; }
public int HttpCode { get; private set; }
public string ApiKey { get; private set; }
```

## 4. 접근 제어 및 캡슐화

### 4.1. 프로퍼티 사용 규칙

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
    public string DisplayName { get; set; } = string.Empty;
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

### 4.2. 읽기 전용 값 규칙

- 초기화 후 값이 변하지 않는 변수는 `readonly`로 선언한다.
- 프로퍼티에 `private init`(C# 9.0)을 최대한 사용한다.

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

## 5. 변수 및 메서드 설계 규칙

### 5.1. 지역 변수 선언 규칙

- 지역 변수는 사용 직전에 선언하여 범위를 최소화한다.
- 같은 범위 안에서는 지역 변수마다 서로 다른 역할이 이름에 분명히 드러나야 한다.

좋지 않은 예:

```cs
public decimal GetDiscountedPrice(Order order, DiscountRate discountRate)
{
    decimal amount;

    amount = order.TotalAmount * discountRate.Value;
    return order.TotalAmount - amount;
}
```

좋은 예:

```cs
public decimal GetDiscountedPrice(Order order, DiscountRate discountRate)
{
    decimal discountAmount = order.TotalAmount * discountRate.Value;
    return order.TotalAmount - discountAmount;
}
```

### 5.2. 메서드 오버로딩 규칙

- 매개 변수 자료형만 다른 모호한 오버로딩은 지양한다.
- 이름만으로도 동작 차이가 드러나야 한다.
- 같은 자료형을 매개 변수로 받지만 의미가 다른 메서드는 이름을 분리한다.

좋지 않은 예:

```cs
public InvoiceAttachment GetAttachment(int index);
public InvoiceAttachment GetAttachment(string fileName);
```

좋은 예:

```cs
public InvoiceAttachment GetAttachmentByIndex(int index);
public InvoiceAttachment GetAttachmentByFileName(string fileName);
```

### 5.3. 기본 매개 변수 규칙

- 기본 매개 변수 대신 메서드 오버로딩을 선호한다.
- 기본 매개 변수를 사용하는 경우 `null`, `false`, `0` 같이 비트 패턴이 0인 값을 사용한다.

좋은 예:

```cs
public enum EInvoiceDeliveryMethod
{
    Email,
    PostalMail,
}

public void SendInvoice(Invoice invoice)
{
    SendInvoice(invoice, EInvoiceDeliveryMethod.Email);
}

public void SendInvoice(Invoice invoice, EInvoiceDeliveryMethod deliveryMethod)
{
    mInvoiceSender.Send(invoice, deliveryMethod);
}
```

허용 가능한 예:

```cs
public IReadOnlyList<Order> GetOrders(CustomerID customerID, int pageNumber = 0)
{
    return mOrderRepository.GetOrders(customerID, pageNumber);
}
```

### 5.4. 변수 가리기 금지

- 변수 가리기(variable shadowing)는 허용하지 않는다.
- 외부 변수가 동일한 이름을 사용 중이라면 내부 변수에는 다른 이름을 사용한다.

좋지 않은 예:

```cs
public class InvoiceService
{
    private int mRetryCount;

    public void SendInvoices(int mRetryCount)
    {
        if (mRetryCount <= 0)
        {
            return;
        }

        SendNextInvoice(mRetryCount);
    }
}
```

좋은 예:

```cs
public class InvoiceService
{
    private int mRetryCount;

    public void SendInvoices(int maxRetryCount)
    {
        int invoiceCount = mInvoiceRepository.GetPendingInvoiceCount();

        for (int i = 0; i < invoiceCount; ++i)
        {
            SendNextInvoice(maxRetryCount);
        }
    }
}
```

### 5.5. 강타입 매개 변수 설계 규칙

- 매개 변수는 가능한 한 강타입으로 받는다.
- 의미를 암묵적으로 담은 `string`, `int`, `long`, `bool`의 직접 사용을 지양한다.
- 정해진 선택지는 `enum`으로 표현한다.
- 도메인 의미가 있는 값은 전용 자료형으로 감싸는 것을 우선 검토한다.
- 전용 자료형은 가능한 한 불변으로 설계한다.
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

public IReadOnlyList<Invoice> GetInvoices(CustomerID customerID, ESortOrder sortOrder)
{
    return mInvoiceRepository.GetInvoices(customerID, sortOrder);
}
```

#### 전용 자료형 사용 예

```cs
public readonly record struct UserID(Guid Value);
public readonly record struct EmailAddress(string Value);
public readonly record struct ProductCode(string Value);

public User GetUserByID(UserID userID)
{
    return mUserRepository.GetByID(userID);
}

public bool SendWelcomeEmail(EmailAddress emailAddress)
{
    return mEmailSender.Send(emailAddress);
}
```

#### 매개 변수 객체 사용 예

```cs
public sealed class OrderSearchCriteria
{
    public CustomerID CustomerID { get; init; }
    public EOrderStatus Status { get; init; }
    public int PageNumber { get; init; }
    public int PageSize { get; init; }
}

public IReadOnlyList<Order> GetOrders(OrderSearchCriteria criteria)
{
    return mOrderRepository.GetOrders(criteria);
}
```

#### bool 매개 변수 지양 예

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

### 5.6. 입력 검증 규칙

- 외부 입력은 시스템 경계에서 검증한다.
- 검증 실패 시 가능한 빨리 반환한다.
- 내부 메서드는 이미 검증된 값을 받는다고 가정한다.

좋은 예:

```cs
private readonly IUserRepository mUserRepository;

public User? GetUserByEmailOrNull(string? emailOrNull)
{
    if (string.IsNullOrWhiteSpace(emailOrNull))
    {
        return null;
    }

    EmailAddress emailAddress = new EmailAddress(emailOrNull);
    return getUserByEmailOrNull(emailAddress);
}

private User? getUserByEmailOrNull(EmailAddress emailAddress)
{
    Debug.Assert(string.IsNullOrWhiteSpace(emailAddress.Value) == false);
    return mUserRepository.GetByEmailOrNull(emailAddress);
}
```

### 5.7. null 처리 규칙

- 메서드의 매개 변수로 `null`을 받지 않도록 설계한다.
  - 특히 `public` 메서드에서는 이 원칙을 우선한다.
- `null` 값을 허용하는 매개 변수를 사용할 경우 변수 이름 뒤에 `OrNull`을 붙인다.
- 메서드에서 `null`을 반환하지 않도록 설계한다.
  - 특히 `public` 메서드에서는 이 원칙을 우선한다.
  - 조회 실패처럼 정상 흐름으로 처리할 수 있는 경우에는 `null` 반환이 필요할 수도 있다.
- 메서드에서 `null`을 반환할 때는 메서드 이름 뒤에 `OrNull`을 붙인다.
- 컬렉션 반환값은 `null` 대신 빈 컬렉션을 반환한다.

좋지 않은 예:

```cs
public Coupon GetCoupon(string? couponCodeOrNull);
public IReadOnlyList<Order>? GetOrdersOrNull(CustomerID customerID);
```

좋은 예:

```cs
public Coupon? GetCouponOrNull(string? couponCodeOrNull)
{
    if (string.IsNullOrWhiteSpace(couponCodeOrNull))
    {
        return null;
    }

    return mCouponRepository.GetByCodeOrNull(couponCodeOrNull);
}

public IReadOnlyList<Order> GetOrders(CustomerID customerID)
{
    IReadOnlyList<Order>? ordersOrNull = mOrderRepository.GetOrdersOrNull(customerID);
    if (ordersOrNull == null)
    {
        return Array.Empty<Order>();
    }

    return ordersOrNull;
}
```

## 6. 제어문 및 assert 규칙

### 6.1. switch 문 규칙

- `switch` 문에는 언제나 `default:` 케이스를 넣는다.
- `switch` 문에서 `default:` 케이스가 절대 실행될 일이 없는 경우, `default:` 안에 `Debug.Fail()`을 추가한다.

좋지 않은 예:

```cs
switch (menuIndex)
{
    case 0:
        OpenHomePage();
        break;
    case 1:
        OpenOrderPage();
        break;
}
```

좋은 예:

```cs
switch (menuIndex)
{
    case 0:
        OpenHomePage();
        break;
    case 1:
        OpenOrderPage();
        break;
    default:
        break;
}
```

도달할 수 없다고 가정하는 경우:

```cs
switch (messageType)
{
    case EMessageType.Text:
        SendTextMessage();
        break;
    case EMessageType.Email:
        SendEmailMessage();
        break;
    default:
        Debug.Fail("unknown message type");
        break;
}
```

### 6.2. assert 사용 규칙

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

## 7. 비동기, 컬렉션, 언어 기능 사용 규칙

### 7.1. 컬렉션 사용 규칙

- `System.Collections`의 비제네릭 컬렉션보다 `System.Collections.Generic`의 제네릭 컬렉션을 사용한다.
- 일반 배열을 사용하는 것도 허용한다.

좋은 예:

```cs
List<Order> completedOrders = new List<Order>();
Dictionary<UserID, User> usersByID = new Dictionary<UserID, User>();
string[] fileNames = new string[10];
```

### 7.2. var 사용 규칙

- `var` 키워드를 사용하지 않는다.
  - 단, LINQ 결과처럼 구체 자료형보다 처리 흐름이 중요한 경우에는 예외를 허용한다.
  - `IEnumerable` 계열 LINQ 결과나 익명 타입이 대표적인 예이다.

좋지 않은 예:

```cs
var order = mOrderRepository.GetByID(orderID);
var users = mUserRepository.GetAll();
```

좋은 예:

```cs
Order order = new Order();
List<User> users = new List<User>();
Dictionary<ProductID, Product> productsByID = new Dictionary<ProductID, Product>();
```

허용 가능한 예외:

```cs
var ordersByCustomerID = orders.GroupBy(order => order.CustomerID);

var summary = new
{
    TotalCount = orders.Count,
    TotalAmount = orders.Sum(order => order.TotalAmount),
};
```

### 7.3. 싱글턴과 static 클래스

- 싱글턴 패턴 대신에 정적(`static`) 클래스를 사용한다.

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

### 7.4. async / await 규칙

- `async void` 대신에 `async Task`를 사용한다.
  - `async void`가 허용되는 유일한 곳은 이벤트 핸들러이다.
- `async` 메서드 이름에 `Async` 접미사를 붙이지 않는다.

좋지 않은 예:

```cs
public async void SaveOrder()
{
    await mOrderRepository.Save(CancellationToken.None);
}

public async Task SaveOrderAsync(CancellationToken cancellationToken)
{
    await mOrderRepository.Save(cancellationToken);
}
```

좋은 예:

```cs
public async Task SaveOrder(CancellationToken cancellationToken)
{
    await mOrderRepository.Save(cancellationToken);
}

private async void handleSaveButtonClick(object sender, EventArgs e)
{
    await SaveOrder(CancellationToken.None);
}
```

### 7.5. 최신 문법 사용 규칙

- 인라인 람다는 한 줄짜리 짧은 코드로 제한한다.
- 객체 초기자(object initializer)를 지양한다.
  - 단, `required` 한정자(C# 11.0)와 초기화 전용 setter(C# 9.0)와 같이 사용할 때는 허용한다.
- 메서드에 전달하는 `out` 매개 변수는 별도의 라인에 선언한다.
  - 인수 목록 안에서 선언하지 않는다.
- null 병합 연산자(C# 2.0)의 사용을 금한다.
- `using` 선언(C# 8.0)의 사용을 금한다.
  - 대신 `using` 문을 사용한다.
- `new` 키워드 뒤에 반드시 명시적으로 자료형을 적는다.
  - 즉, C# 9.0의 `new()` 사용을 금지한다.
  - 단, 메서드 내부에서 익명 타입을 사용할 때는 허용한다.
- 범용적인 값을 강타입(strong type)으로 만들 때는 `readonly record struct`(C# 10.0)를 사용한다.

좋지 않은 예:

```cs
orders.ForEach(order =>
{
    ReserveInventory(order);
    SendOrderConfirmationEmail(order);
});

using StreamReader reader = new StreamReader(filePath);

bool isParsed = int.TryParse(text, out int value);

User user = new();
```

좋은 예:

```cs
foreach (Order order in orders)
{
    PrintOrder(order);
}

using (StreamReader reader = new StreamReader(filePath))
{
    string fileContent = reader.ReadToEnd();
}

int value;
bool isParsed = int.TryParse(text, out value);

User user = new User();

public readonly record struct OrderID(Guid Value);
```

## 8. 멤버 구성 및 순서

클래스 내부 구성은 다음 순서를 따른다.

1. 멤버 변수
2. 프로퍼티
   - 단, 프로퍼티와 대응하는 `private` 멤버 변수는 프로퍼티 바로 위에 적는다.
3. 생성자
4. 메서드
   - public -> private 순서로 배치한다.

추가 원칙은 다음과 같다.

- 공개 메서드가 먼저 보이도록 배치한다.
- 필요에 따라 관련된 멤버끼리 논리적으로 묶어 배치할 수 있다.
- `private` 헬퍼 메서드는 이를 사용하는 `public` 메서드 아래쪽에 배치한다.
- 멤버 변수도 연관 있는 것끼리 그룹을 짓는다.

좋은 예:

```cs
public class UserService
{
    private readonly IUserRepository mUserRepository;
    private readonly IEmailSender mEmailSender;
    private bool mHasPendingRequest;

    public int UserCount
    {
        get;
        private set;
    }

    public UserService(IUserRepository userRepository, IEmailSender emailSender)
    {
        mUserRepository = userRepository;
        mEmailSender = emailSender;
    }

    public User GetUserByID(UserID userID)
    {
        return mUserRepository.GetByID(userID);
    }

    public bool SendWelcomeEmail(UserID userID)
    {
        User user = GetUserByID(userID);
        return sendWelcomeEmailInternal(user);
    }

    private bool sendWelcomeEmailInternal(User user)
    {
        return mEmailSender.Send(user.EmailAddress);
    }
}
```

## 9. 포맷팅 규칙

### 9.1. 들여쓰기 규칙

- 탭(tab)은 Visual Studio 기본값을 사용한다.
- Visual Studio를 사용하지 않을 때는 공백 4칸을 들여쓰기 기준으로 사용한다.

### 9.2. 중괄호 사용 규칙

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

### 9.3. 선언 규칙

- 한 줄에 변수 하나만 선언한다.

좋지 않은 예:

```cs
int counter = 0, index = 0;
```

좋은 예:

```cs
int counter = 0;
int index = 0;
```

## 10. 프로젝트 설정 관련

- 배포(release) 빌드에서 컴파일러 경고(warning)를 오류(error)로 처리하게 설정한다.
- implicit global using(C# 10.0)을 사용하지 않는다.
- nullable context를 사용한다.

## 11. 특정 프레임워크 및 패턴용 가이드라인

### 11.1. 자동 직렬화 (예: `System.Text.Json`)

- 자동 직렬화에 사용할 데이터는 `class`로 정의한다.
- 자동 직렬화용 `class` 파일에는 특정 직렬화 라이브러리 전용 애트리뷰트를 넣지 않는다.
- 자동 직렬화용 `class`의 데이터는 언제나 `public` 자동 프로퍼티를 사용한다.
  - 프로퍼티와 직렬화 데이터의 1:1 대응 관계를 유지한다.
- 자동 직렬화용 `class`에 읽기 전용 프로퍼티가 필요한 경우, 그 대신 `public` 메서드를 만든다.
- 자동 직렬화용 `class`의 `public` 생성자는 언제나 하나만 두며, 이 생성자는 매개 변수를 받지 않는다.
- 자동 직렬화 메서드(예: `JsonSerializer.Serialize<>()`)를 직접 호출하지 않는다.
  - 래퍼 메서드를 만들어 자동 직렬화 메서드에 넣을 수 있는 매개 변수의 자료형을 통제한다.

좋은 예:

```cs
public class UserResponse
{
    public string DisplayName { get; set; } = string.Empty;
    public string EmailAddress { get; set; } = string.Empty;

    public UserResponse()
    {
    }

    public bool HasEmailAddress()
    {
        return string.IsNullOrWhiteSpace(EmailAddress) == false;
    }
}
```

### 11.2. XAML 컨트롤

- 컨트롤의 이름(예: `x:Name`)은 코드에서 직접 참조할 때만 만든다.
- 컨트롤의 변수 이름 앞에는 `x`를 붙인다.
- 컨트롤의 변수 이름 앞에는 컨트롤 종류도 붙인다.

좋은 예:

```cs
xLabelUserName
xButtonSubmit
xTextBoxEmailAddress
xGridLayoutRoot
```

### 11.3. ASP.NET Core

- REST API의 요청 바디(request body)용 DTO(Data Transfer Object)를 만들 때, 각 입력 프로퍼티를 `nullable`로 선언하여 모델 유효성 검증(model validation)이 자동으로 이뤄지게 한다.

좋은 예:

```cs
public class UpdateUserRequest
{
    [Required]
    public Guid? UserID { get; set; }

    [Required]
    public string? DisplayName { get; set; }
}
```

- 컨트롤러 메서드에서 요청을 처리할 때 가장 먼저 할 일은 입력값 검증이다.
- 이 검증을 통과하면 모든 입력값은 유효한 것으로 간주한다.
  - 따라서 `[Required]` 애트리뷰트가 달리고 `nullable`로 선언한 프로퍼티는 그 후부터 모두 `null`이 아니다.
- 위 원칙과는 달리 `[FromRoute]`처럼 라우트에서 바인딩되는 매개 변수에는 `?`를 붙이지 않는다.

좋은 예:

```cs
public UserResponse? GetUserOrNull([FromRoute] Guid userID)
{
    if (ModelState.IsValid == false)
    {
        return null;
    }

    User? userOrNull = mUserService.GetUserByIDOrNull(new UserID(userID));
    if (userOrNull == null)
    {
        return null;
    }

    return mUserResponseFactory.Create(userOrNull);
}
```
