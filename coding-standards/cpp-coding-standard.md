# C++ 코딩 표준

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
  - 함수의 매개 변수와 반환값은 가능한 한 강타입으로 설계한다.
  - 문자열, 숫자, 불리언으로 의미를 암묵적으로 전달하는 설계를 지양한다.
- 실패 조건은 가능한 한 빠르고 분명하게 처리한다.
  - 외부 입력은 시스템 경계에서 검증한다.
  - 내부 로직은 검증된 값과 도메인 자료형을 받도록 구성한다.
  - 내부 함수의 사전 조건이 깨지면 즉시 실패가 드러나게 한다.
- C++ 코드는 소유권과 수명을 명확하게 드러내야 한다.
  - 객체를 누가 생성하고, 누가 보관하고, 누가 해제하는지 코드에서 확인할 수 있어야 한다.
  - 포인터, 참조, 스마트 포인터는 의미에 따라 구분해서 사용한다.

이 코딩 표준은 다음 자료를 참고하되, 실제 개발 환경에서의 가독성과 유지보수성을 고려하여 일부 항목을 현실적으로 조정하였다.

- [Google C++ Style Guide](https://google.github.io/styleguide/cppguide.html)
- [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)
- [LLVM Coding Standards](https://llvm.org/docs/CodingStandards.html)

## 2. 파일, `include`, 네임스페이스, 클래스 구조

### 2.1. 헤더 파일 구성 순서

C++ 헤더 파일은 다음 순서로 구성한다.

1. 라이선스 또는 저작권 주석
2. `#pragma once`
3. 필요한 외부 라이브러리 헤더
4. 필요한 프로젝트 내부 헤더
5. 네임스페이스 선언
6. 전방 선언
7. 클래스, 구조체, 열거형 또는 함수 선언

각 영역 사이에는 빈 줄 하나를 둔다.
라이선스 또는 저작권 주석이 없으면 `#pragma once`를 파일 첫 줄에 둔다.

```cpp
#pragma once

#include "order/OrderId.hpp"

namespace order
{
    class OrderRepository;

    class OrderService
    {
    public:
        explicit OrderService(OrderRepository& repository);

        void SubmitOrder(const OrderId& orderId);

    private:
        OrderRepository& mRepository;
    };
}
```

### 2.2. 소스 파일 구성 순서

C++ 소스 파일은 다음 순서로 구성한다.

1. 라이선스 또는 저작권 주석
2. 필요한 외부 라이브러리 헤더
3. 필요한 프로젝트 내부 헤더
4. 네임스페이스 선언
5. 함수와 멤버 함수 정의

```cpp
#include "order/OrderRepository.hpp"
#include "order/OrderService.hpp"

namespace order
{
    void OrderService::SubmitOrder(const OrderId& orderId)
    {
        mRepository.Submit(orderId);
    }
}
```

### 2.3. `include` 규칙

- 표준 라이브러리와 외부 라이브러리 헤더는 꺾쇠괄호(`<>`)로 포함한다.
- 프로젝트 내부 헤더는 큰따옴표(`""`)로 포함한다.
- `include`는 외부 헤더와 프로젝트 내부 헤더로 나누고, 외부 헤더를 먼저 둔다.
- 그룹 사이에는 빈 줄 하나를 둔다.
- 각 그룹 안에서는 알파벳 순서로 정렬한다.
- 사용하지 않는 헤더는 남기지 않는다.
- 헤더 파일에서 `using namespace`를 사용하지 않는다.

좋은 예:

```cpp
#include <string>
#include <vector>

#include "order/OrderId.hpp"
#include "order/OrderRepository.hpp"
#include "order/OrderService.hpp"
```

### 2.4. 헤더 독립성 규칙

- 공개 헤더는 다른 파일의 include 순서에 의존하지 않게 한다.
- 헤더에서 선언에 필요한 타입은 직접 포함하거나 전방 선언으로 해결한다.
- 공개 헤더에는 내부 구현 세부 사항과 플랫폼별 세부 사항을 노출하지 않는다.
- 헤더 안의 함수 정의는 짧은 접근자, 템플릿, `constexpr`처럼 헤더에 있어야 하는 경우로 제한한다.
- 헤더에 정의하는 함수나 변수는 `ODR` 위반이 발생하지 않도록 `inline` 또는 템플릿 규칙을 지킨다.
- 공개 API와 내부 구현 세부 사항은 분명히 구분한다.

좋은 예:

```cpp
#pragma once

#include <vector>

#include "order/Order.hpp"

namespace order
{
    class OrderService
    {
    public:
        std::vector<Order> GetOrders() const;
    };
}
```

### 2.5. 전방 선언 규칙

- 포인터나 참조만 사용하는 타입은 헤더에서 전방 선언한다.
- 값으로 보관하거나 상속하거나 멤버 함수 본문에서 사용하는 타입은 헤더를 포함한다.
- 전방 선언으로 인해 타입의 소유권이나 의존성이 불분명해지면 헤더를 포함한다.
- 소유하지 않은 외부 라이브러리 타입은 전방 선언하지 않는다.
- 전방 선언을 위해 설계를 더 복잡하게 만들지 않는다.

좋은 예:

```cpp
namespace payment
{
    class PaymentGateway;
}

namespace order
{
    class OrderService
    {
    public:
        explicit OrderService(payment::PaymentGateway& paymentGateway);

    private:
        payment::PaymentGateway& mPaymentGateway;
    };
}
```

### 2.6. 네임스페이스 규칙

- 네임스페이스 이름은 모두 소문자로 작성한다.
- 회사, 제품, 도메인, 기능 순서처럼 큰 범위에서 작은 범위로 내려가도록 작성한다.
- 네임스페이스 이름에 밑줄(`_`)이나 대문자를 사용하지 않는다.
- 네임스페이스 전체를 가져오는 `using namespace`는 사용하지 않는다.
- `using` 선언과 네임스페이스 별칭은 필요한 범위에서만 사용하고, 헤더 파일에는 두지 않는다.
- `.cpp` 파일에서만 사용하는 함수, 상수, 타입은 익명 네임스페이스에 둔다.
- 헤더 파일에서는 익명 네임스페이스를 사용하지 않는다.

```cpp
namespace awesome::shop::order
{
}
```

파일 내부 전용 구현 예:

```cpp
namespace
{
    constexpr int MAX_RETRY_COUNT = 3;

    bool IsRetryable(EOrderError error)
    {
        return error == EOrderError::TIMEOUT;
    }
}
```

### 2.7. 클래스와 파일 구조

- 하나의 클래스는 기본적으로 하나의 헤더 파일과 하나의 소스 파일로 구성한다.
- 파일 이름은 대소문자까지 포함해서 클래스 이름과 일치해야 한다.
- 작은 보조 타입이 필요하면 중첩 타입으로 두거나 별도 파일로 분리한다.
- 하나의 클래스 구현을 여러 파일로 나눌 때는 클래스 이름 뒤에 밑줄과 책임을 드러내는 이름을 붙인다.
- 플랫폼별 구현 파일은 플랫폼 이름을 파일 이름에 명시한다.
- 공개 헤더는 공개 API만 담고, 내부 구현 보조 타입은 `.cpp` 파일이나 비공개 헤더에 둔다.

좋은 예:

```text
OrderService.hpp
OrderService.cpp
OrderService_validate.cpp
OrderService_serialize.cpp
FileSystem_windows.cpp
FileSystem_mac.cpp
```

### 2.8. 전처리기 사용 규칙

- 전처리기는 포함 방지, 플랫폼 분기, 빌드 옵션처럼 필요한 곳에만 사용한다.
- 플랫폼별 `#if`는 가능한 한 좁은 범위에 둔다.
- 공개 헤더에 플랫폼 세부 사항이 퍼지지 않도록 한다.
- 여러 줄 매크로는 함수나 템플릿으로 대체할 수 없는 경우에만 사용한다.

좋은 예:

```cpp
std::filesystem::path GetConfigDirectory()
{
#if AWESOME_PLATFORM_WINDOWS
    return GetWindowsConfigDirectory();
#else
    return GetUnixConfigDirectory();
#endif
}

template <typename T>
constexpr T Max(T left, T right)
{
    return left < right ? right : left;
}
```

## 3. 네이밍 규칙

### 3.1. 클래스, 인터페이스, 구조체, 열거형 이름 규칙

- 클래스는 `PascalCase`를 사용한다.
- 클래스 이름은 명사 또는 명사구로 작성한다.
- 인터페이스는 `PascalCase`를 사용하며, 이름 앞에 `I` 접두어를 사용한다.
- 구조체는 `PascalCase`를 사용한다.
- 열거형은 `PascalCase`를 사용하며, 이름 앞에 `E` 접두어를 사용한다.
- 비트 플래그나 권한 조합처럼 플래그 성격의 열거형에는 `Flags` 접미사를 붙인다.
- 열거형 멤버는 모두 대문자와 밑줄을 사용한다.
- C 스타일 `enum`보다 `enum class`를 사용한다.
- 열거형의 기반 타입은 저장 크기, 직렬화, `ABI` 같은 이유가 있을 때만 명시한다.
- 비트 플래그 열거형은 플래그 연산자를 명시적으로 제공한다.

좋은 예:

```cpp
class OrderService
{
};

class IPaymentGateway
{
};

struct OrderSummary
{
    std::uint64_t OrderId;
    std::uint32_t TotalPriceInCents;
};

enum class EOrderStatus
{
    PENDING,
    PAID,
    CANCELED
};
```

기반 타입이 필요한 경우:

```cpp
enum class EPacketType : std::uint8_t
{
    LOGIN = 1,
    MOVE = 2,
    LOGOUT = 3
};
```

비트 플래그를 사용하는 경우:

```cpp
enum class EPermissionFlags : std::uint8_t
{
    NONE = 0,
    READ = 1 << 0,
    WRITE = 1 << 1,
    DELETE = 1 << 2
};

inline EPermissionFlags operator|(EPermissionFlags left, EPermissionFlags right)
{
    const std::uint8_t leftValue = static_cast<std::uint8_t>(left);
    const std::uint8_t rightValue = static_cast<std::uint8_t>(right);

    return static_cast<EPermissionFlags>(leftValue | rightValue);
}
```

### 3.2. 함수와 멤버 함수 이름 규칙

- 함수 이름은 반드시 동사 또는 동사구로 시작한다.
- 가능하면 `동사 + 목적어` 형태로 작성한다.
- 반환값이 있는 함수는 무엇을 반환하는지가 이름에 드러나야 한다.
- `bool` 값을 반환하는 함수는 `Is`, `Has`, `Can`, `Should`를 우선 사용한다.
  - 이 표현이 부자연스러운 경우에는 상태를 나타내는 다른 3인칭 단수형 동사를 사용한다.
- 재귀 함수는 이름 끝에 `Recursive`를 붙인다.
- 공개 멤버 함수와 자유 함수 이름은 `PascalCase`를 사용한다.
- 공개 멤버 함수가 아닌 멤버 함수 이름은 `camelCase`를 사용한다.

좋은 예:

```cpp
class OrderService
{
public:
    EOrderStatus GetOrderStatus() const;
    bool CanSubmitOrder(OrderId orderId) const;

private:
    void validateOrderTreeRecursive(const OrderNode& node) const;
};
```

### 3.3. 변수 이름 규칙

- 지역 변수와 함수의 매개 변수 이름은 `camelCase`를 사용한다.
- 변수 이름은 명사 또는 명사구를 사용한다.
- 값의 의미, 역할, 상태, 개수, 위치가 드러나야 한다.
- 의미 없는 축약어, 임의의 약어, 단일 문자 변수 이름은 사용하지 않는다.
  - 단, 짧은 반복문 인덱스처럼 관례적으로 명확한 경우는 예외로 한다.
- 단순 반복문 변수가 아닌 경우 `i`, `e` 같은 변수 이름 대신 `index`, `employee`처럼 변수에 담긴 값을 바로 알 수 있는 이름을 사용한다.
- 컨테이너는 복수형으로 작성한다.
- 키-값 대응 컨테이너는 가능하면 `xxxByYyy` 형태를 사용한다.
- `bool` 변수는 `is`, `has`, `can`, `should`를 우선 사용한다.
- 출력 매개 변수는 이름 앞에 `out` 접두어를 사용한다.

좋은 예:

```cpp
const int retryCount = order.GetRetryCount();
const int currentPage = pageRequest.GetPage();
const std::string invoiceFilePath = invoice.GetFilePath();

const std::vector<Order> pendingOrders = orderRepository.FindPendingOrders();
const std::unordered_map<OrderId, Order> ordersById = BuildOrdersById(orders);

const bool isPaymentApproved = payment.IsApproved();
const bool hasDeliveryAddress = order.HasDeliveryAddress();
const bool canRetry = retryPolicy.CanRetry();
const bool shouldUpdateCache = cachePolicy.ShouldUpdate();
```

### 3.4. 멤버 변수 이름 규칙

- 인스턴스 멤버 변수는 앞에 `m`을 붙이고 `PascalCase`를 사용한다.
- 정적 멤버 변수는 앞에 `s`를 붙이고 `PascalCase`를 사용한다.
- 접두어 뒤에는 멤버 변수의 원래 이름을 대문자로 시작하여 이어 붙인다.
- `bool` 멤버 변수는 `mIs`, `mHas`, `mCan`, `mShould` 형태를 우선 사용한다.
- 멤버 변수 이름은 지역 변수나 매개 변수 이름과 충돌하지 않아야 한다.
- 상수와 상수 역할을 하는 정적 값은 `s` 접두어를 사용하지 않고, 3.5의 상수 이름 규칙을 따른다.

좋은 예:

```cpp
class UserProfile
{
private:
    int mRetryCount;
    std::string mUserName;
    bool mIsEnabled;
    bool mHasPermission;

    static std::string sCurrentTimeZone;
};
```

### 3.5. 상수와 매크로 이름 규칙

- 컴파일 시간 상수는 모두 대문자와 밑줄을 사용한다.
- 매크로는 모두 대문자와 밑줄을 사용한다.
- 매크로보다 `constexpr`, `const`, `enum class`, 인라인 함수를 우선 사용한다.
- 함수형 매크로가 불가피하면 인자와 전체 표현식을 괄호로 감싼다.
- 매크로 이름은 네임스페이스의 보호를 받지 않으므로 충돌 가능성이 낮은 이름을 사용한다.

좋은 예:

```cpp
constexpr int DEFAULT_PAGE_SIZE = 20;
constexpr int MAX_RETRY_COUNT = 3;
```

매크로가 필요한 경우:

```cpp
#if defined(_WIN32)
#define AWESOME_PLATFORM_WINDOWS 1
#else
#define AWESOME_PLATFORM_WINDOWS 0
#endif
```

함수형 매크로가 필요한 경우:

```cpp
#define AWESOME_LOG_ERROR(message) (AwesomeLogError((message), __FILE__, __LINE__))
```

## 4. 멤버 구성 및 순서

멤버는 다음 순서로 작성한다.

1. `friend` 선언
2. 공개 생성자와 소멸자
3. 공개 멤버 함수
4. 보호 멤버 함수
5. 비공개 멤버 함수
6. 보호 멤버 변수
7. 비공개 멤버 변수

- 접근 지정자는 같은 종류의 멤버를 묶을 때 사용한다.
- 같은 접근 지정자를 필요 이상으로 반복해서 클래스 구조를 흐리지 않는다.

좋지 않은 예:

```cpp
class OrderService
{
private:
    OrderRepository& mRepository;

public:
    void SubmitOrder(OrderId orderId);

private:
    bool canSubmitOrder(OrderId orderId) const;

public:
    explicit OrderService(OrderRepository& repository);
};
```

좋은 예:

```cpp
class OrderService
{
public:
    explicit OrderService(OrderRepository& repository);

    void SubmitOrder(OrderId orderId);

private:
    bool canSubmitOrder(OrderId orderId) const;

    OrderRepository& mRepository;
};
```

## 5. 접근 제어 및 캡슐화

### 5.1. 접근 지정자 규칙

- 멤버 변수는 기본적으로 `private`으로 둔다.
- `public` 멤버 변수는 사용하지 않는다.
- 외부에서 필요한 동작만 `public`으로 공개한다.
- 상속을 위한 확장 지점만 `protected`로 둔다.
- 상속을 염두에 두지 않은 클래스는 `final` 사용을 고려한다.

좋은 예:

```cpp
class Order
{
public:
    EOrderStatus GetStatus() const;
    std::uint32_t GetTotalPriceInCents() const;

private:
    EOrderStatus mStatus;
    std::uint32_t mTotalPriceInCents;
};
```

### 5.2. getter와 setter 사용 규칙

- 단순히 멤버 변수를 노출하기 위한 getter와 setter는 지양한다.
- 객체의 불변식이 유지되는 경우에만 setter를 제공한다.
- 읽기만 필요한 값은 getter만 제공한다.
- 값 변경에 검증이나 부가 동작이 필요하면 동작 이름으로 표현한다.

좋은 예:

```cpp
class Order
{
public:
    void MarkAsPaid(PaymentId paymentId);
    void Cancel(CancelReason reason);

    EOrderStatus GetStatus() const;
};
```

### 5.3. 구조체 사용 규칙

- `struct`는 순수한 데이터 묶음에만 사용한다.
- `struct`의 필드는 `PascalCase`를 사용한다.
- `struct`에는 사용자 정의 생성자, 소멸자, 가상 함수를 넣지 않는다.
- `struct`에는 비공개 상태나 동작을 넣지 않는다.
- `struct`는 메모리 블록 복사가 가능한 형태(`trivially copyable`)로 유지한다.
- 불변식, 검증, 동작이 필요하면 `class`로 표현한다.

좋지 않은 예:

```cpp
struct Order
{
public:
    Order(int totalPrice);
    virtual ~Order();

private:
    int mTotalPrice;

public:
    void Submit();
};
```

좋은 예:

```cpp
struct OrderSummary
{
    std::uint64_t OrderId;
    std::uint32_t TotalPriceInCents;
    EOrderStatus Status;
};
```

### 5.4. `const` 멤버 함수 규칙

- 멤버 함수는 읽기 전용을 기본으로 설계한다.
- 객체 상태를 변경하지 않는 멤버 함수에는 반드시 `const`를 붙인다.
- 객체 상태를 변경해야 하는 경우에만 `const`를 제거한다.
- 논리적으로 읽기 전용인 함수는 내부 캐시를 사용하더라도 호출자 관점의 불변성을 유지해야 한다.

좋은 예:

```cpp
class Order
{
public:
    EOrderStatus GetStatus() const;
};
```

### 5.5. 상속과 가상 함수 규칙

- 재정의하는 가상 함수에는 언제나 `override`를 붙인다.
- 더 이상 상속되면 안 되는 클래스나 가상 함수에는 `final`을 붙인다.
- `override`와 `final`을 함께 사용할 때는 `override final` 순서로 작성한다.

좋은 예:

```cpp
class IPaymentGateway
{
public:
    virtual ~IPaymentGateway() = default;

    virtual PaymentResult ApprovePayment(const PaymentRequest& request) = 0;
};

class HttpPaymentGateway final : public IPaymentGateway
{
public:
    PaymentResult ApprovePayment(const PaymentRequest& request) override;
};

class CachingPaymentGateway : public IPaymentGateway
{
public:
    PaymentResult ApprovePayment(const PaymentRequest& request) override final;
};
```

### 5.6. 멤버 초기화 규칙

- 생성자에서는 멤버 초기화 목록을 사용한다.
- 멤버 선언 순서와 초기화 목록 순서를 일치시킨다.
- 생성자 본문에서 대입으로 초기화하지 않는다.
- 대입을 막기 위한 목적으로 `const` 멤버 변수나 참조 멤버 변수를 남용하지 않는다.

좋지 않은 예:

```cpp
class OrderService
{
public:
    OrderService(OrderRepository& repository)
    {
        mRepository = &repository;
    }

private:
    OrderRepository* mRepository;
};
```

좋은 예:

```cpp
class OrderService
{
public:
    explicit OrderService(OrderRepository& repository)
        : mRepository(repository)
    {
    }

private:
    OrderRepository& mRepository;
};
```

## 6. 변수 및 함수 설계 규칙

### 6.1. 지역 변수 선언 규칙

- 지역 변수는 사용 직전에 선언하여 범위를 최소화한다.
- 값은 원칙적으로 읽기 전용으로 선언하고, 바뀌지 않는 지역 변수에는 `const`를 붙인다.
- 하나의 선언문에는 하나의 변수만 선언한다.
- 초기화 없이 변수를 선언하지 않는다.

좋지 않은 예:

```cpp
void SubmitOrder(const OrderRequest& request)
{
    OrderId orderId;
    UserId userId;

    userId = request.GetUserId();
    orderId = request.GetOrderId();

    Submit(userId, orderId);
}
```

좋은 예:

```cpp
void SubmitOrder(const OrderRequest& request)
{
    const UserId userId = request.GetUserId();
    const OrderId orderId = request.GetOrderId();

    Submit(userId, orderId);
}
```

### 6.2. 긴 표현식 분리 규칙

- 인덱스, 오프셋, 크기, 주소 계산처럼 여러 의미가 섞인 계산을 하나의 표현식에 몰아넣지 않는다.
- 표현식이 길어지면 의미 있는 계산 단위로 나누어 지역 변수에 담는다.
- 중간 계산값은 가능한 한 `const` 지역 변수로 선언한다.
- 중간 변수 이름은 계산 방식보다 값의 의미를 드러내야 한다.
- 단순히 줄 길이를 줄이기 위한 `temp`, `value`, `result` 같은 이름은 사용하지 않는다.
- 한눈에 이해되는 단순 표현식은 불필요하게 나누지 않는다.

좋지 않은 예:

```cpp
const std::size_t sourceByteIndex = static_cast<std::size_t>(region.Y + y) * static_cast<std::size_t>(mBytesPerRow) + static_cast<std::size_t>(region.X + x) * static_cast<std::size_t>(mChannelCount) + channelIndex;
```

좋은 예:

```cpp
const std::size_t sourceRowOffset = static_cast<std::size_t>(region.Y + y) * static_cast<std::size_t>(mBytesPerRow);
const std::size_t sourceColumnOffset = static_cast<std::size_t>(region.X + x) * static_cast<std::size_t>(mChannelCount);

const std::size_t sourceByteIndex = sourceRowOffset + sourceColumnOffset + channelIndex;
```

### 6.3. 함수 오버로딩 규칙

- 함수 오버로딩은 의미가 같고 입력 형태만 다를 때만 사용한다.
- 반환형만 다른 오버로딩은 사용할 수 없다.
- 이름이 같은 함수가 서로 다른 책임을 수행하지 않게 한다.
- `const` 객체와 비 `const` 객체를 모두 지원하기 위한 오버로딩은 허용한다.

좋지 않은 예:

```cpp
class UserRepository
{
public:
    User Search(UserId userId) const;
    std::vector<User> Search(std::string_view keyword) const;
};
```

좋은 예:

```cpp
class UserRepository
{
public:
    User FindById(UserId userId) const;
    std::vector<User> SearchByKeyword(std::string_view keyword) const;
};
```

### 6.4. 기본 매개 변수 규칙

- 기본 매개 변수보다 명시적인 오버로드를 우선 사용한다.
- 기본 매개 변수를 사용할 때는 호출자가 생략해도 의미가 분명한 값만 사용한다.
- 기본값은 `0`, `nullptr`, 빈 객체처럼 비트 수준에서 단순한 값을 우선한다.
- 헤더에 노출되는 기본 매개 변수는 변경 시 호출 코드의 의미가 바뀔 수 있으므로 신중하게 사용한다.

좋지 않은 예:

```cpp
void ExportReport(bool shouldIncludeMetadata = true, bool shouldCompress = false);
```

좋은 예:

```cpp
void ExportReport();
void ExportReportWithMetadata();
void ExportCompressedReport();
```

### 6.5. 변수 가리기 금지

- 안쪽 범위의 이름이 바깥쪽 범위의 이름을 가리지 않게 한다.
- 멤버 변수와 매개 변수의 이름이 충돌하지 않게 한다.
- 컴파일러 경고에서 변수 가리기를 감지할 수 있으면 경고를 활성화한다.

좋지 않은 예:

```cpp
class OrderService
{
public:
    explicit OrderService(OrderRepository& mRepository)
        : mRepository(mRepository)
    {
    }

private:
    OrderRepository& mRepository;
};
```

좋은 예:

```cpp
class OrderService
{
public:
    explicit OrderService(OrderRepository& repository)
        : mRepository(repository)
    {
    }

private:
    OrderRepository& mRepository;
};
```

### 6.6. 강타입 매개 변수 설계 규칙

- 매개 변수는 가능한 한 강타입으로 받는다.
- 의미를 암묵적으로 담은 정수, 문자열, 불리언의 직접 사용을 지양한다.
- 정해진 선택지는 `enum class`로 표현한다.
- 도메인 의미가 있는 값은 전용 자료형으로 감싸는 것을 우선 검토한다.
- 전용 자료형은 가능한 한 불변으로 설계한다.
- 값으로 반환하는 타입에는 `const`를 붙이지 않는다.
- 관련된 여러 매개 변수는 별도의 객체로 묶는 것을 우선 검토한다.
- 의미가 다른 동일 자료형 매개 변수를 나란히 받는 설계를 피한다.
- `bool` 매개 변수는 호출부 가독성을 떨어뜨리므로 가능한 한 사용하지 않는다.

좋지 않은 예:

```cpp
void SetPaymentStatus(int status);
void FindOrder(std::uint64_t id);
void SaveOrder(OrderId orderId, bool shouldSendNotification);
void CreateOrder(UserId userId, AddressId addressId, PaymentMethodId paymentMethodId);
```

좋은 예:

```cpp
class OrderId
{
public:
    explicit OrderId(std::uint64_t value);

    std::uint64_t GetValue() const;

private:
    std::uint64_t mValue;
};

enum class EPaymentStatus
{
    PENDING,
    PAID,
    FAILED
};

class CreateOrderRequest
{
public:
    CreateOrderRequest(
        UserId userId,
        AddressId addressId,
        PaymentMethodId paymentMethodId
    );

    UserId GetUserId() const;
    AddressId GetAddressId() const;
    PaymentMethodId GetPaymentMethodId() const;

private:
    UserId mUserId;
    AddressId mAddressId;
    PaymentMethodId mPaymentMethodId;
};

void SetPaymentStatus(EPaymentStatus status);
void FindOrder(OrderId orderId);
void SaveOrderAndNotify(OrderId orderId);
void CreateOrder(const CreateOrderRequest& request);
```

### 6.7. 형변환 규칙

- C 스타일 캐스트를 사용하지 않는다.
- 의미에 맞는 C++ 캐스트를 사용한다.
- `const_cast`는 직접 사용하지 않는다.
- 타입 설계나 API 경계를 수정할 수 있다면 캐스트보다 설계를 고친다.

좋지 않은 예:

```cpp
const std::uint8_t packetType = (std::uint8_t)EPacketType::LOGIN;
const OrderMessage* orderMessage = (const OrderMessage*)message;
```

좋은 예:

```cpp
const std::uint8_t packetType = static_cast<std::uint8_t>(EPacketType::LOGIN);

const OrderMessage& orderMessage = message.AsOrderMessage();
```

### 6.8. 컨테이너 사용 규칙

- 크기 상한이 분명한 데이터는 고정 크기 컨테이너를 우선한다.
- 크기가 변하는 데이터에는 표준 컨테이너를 사용한다.
- 동적 컨테이너에 많은 값을 추가할 때는 필요한 크기를 미리 예약한다.
- 컨테이너를 반환할 때는 널 포인터 대신 빈 컨테이너를 반환한다.
- 컨테이너를 읽기만 하는 함수는 `const` 참조로 받는다.
- 컨테이너 내부 원소의 포인터나 참조를 외부에 오래 보관하지 않는다.

좋은 예:

```cpp
std::vector<Order> GetOrders();
constexpr std::size_t POSITION_COMPONENT_COUNT = 3;
const std::array<float, POSITION_COMPONENT_COUNT> position = { 0.0f, 0.0f, 0.0f };

void AddOrders(std::vector<Order>& orders, const std::vector<Order>& newOrders)
{
    orders.reserve(orders.size() + newOrders.size());
    orders.insert(orders.end(), newOrders.begin(), newOrders.end());
}
```

### 6.9. 입력 검증 규칙

- 외부 입력은 시스템 경계에서 검증한다.
- 검증 실패는 시스템 경계에서 즉시 처리한다.
- 내부 함수는 이미 검증된 값과 도메인 자료형을 받는다고 가정한다.
- 내부 함수의 사전 조건이 깨지면 즉시 실패가 드러나게 한다.
- 검증 실패는 이 문서의 오류 처리 방식과 일관되게 표현한다.

좋지 않은 예:

```cpp
void OrderService::SubmitOrder(const std::string& orderIdText)
{
    submitOrderInternal(orderIdText);
}

void OrderService::submitOrderInternal(const std::string& orderIdText)
{
    if (orderIdText.empty())
    {
        return;
    }

    SubmitParsedOrder(orderIdText);
}
```

좋은 예:

```cpp
void OrderController::SubmitOrder(const std::string& orderIdText)
{
    const std::optional<OrderId> orderId = OrderId::TryParse(orderIdText);
    if (!orderId.has_value())
    {
        SendBadRequest("orderId is invalid");
        return;
    }

    mOrderService.SubmitOrder(*orderId);
}

void OrderService::SubmitOrder(OrderId orderId)
{
    Assert(orderId.IsValid());

    mOrderRepository.Submit(orderId);
}
```

### 6.10. 오류 처리 방식 규칙

- 파싱, 조회, 변환처럼 실패 가능성이 정상 흐름인 함수에는 `Try` 이름을 사용할 수 있다.
- `Try` 함수가 값 하나의 존재 여부만 표현하면 `std::optional<T>`를 반환한다.
- `bool + out` 패턴은 출력 객체 재사용, 여러 결과 기록, 성능상 이유, 기존 API 호환처럼 이유가 분명할 때만 사용한다.
- 같은 모듈에서는 `std::optional<T>` 반환과 `bool + out` 패턴을 같은 의미로 섞지 않는다.
- 실패 원인을 호출자에게 전달해야 하면 오류 코드나 전용 결과 타입처럼 호출자가 처리할 수 있는 형태로 반환한다.

`std::optional<T>` 반환을 사용하는 경우:

```cpp
std::optional<OrderId> TryParseOrderId(std::string_view text)
{
    std::uint64_t value = 0;
    const char* textBegin = text.data();
    const char* textEnd = text.data() + text.size();
    const std::from_chars_result parseResult = std::from_chars(textBegin, textEnd, value);

    if (parseResult.ec != std::errc() || parseResult.ptr != textEnd)
    {
        return std::nullopt;
    }

    return OrderId(value);
}
```

출력 객체를 재사용해야 하는 경우:

```cpp
bool PacketReader::TryReadPayload(PacketBuffer* outPayloadBuffer, std::size_t* outByteCount)
{
    Assert(outPayloadBuffer != nullptr);
    Assert(outByteCount != nullptr);

    outPayloadBuffer->Clear();
    *outByteCount = 0;

    if (!CanReadPayload())
    {
        return false;
    }

    *outByteCount = ReadPayloadInto(*outPayloadBuffer);
    return true;
}
```

## 7. 제어문 규칙

### 7.1. 빠른 반환 규칙

- 실패 조건이나 중단 조건은 앞에서 처리한다.
- 정상 흐름이 깊은 중첩 안에 들어가지 않도록 한다.
- 빠른 반환을 사용할 때도 반환값의 의미가 분명해야 한다.

좋은 예:

```cpp
void SubmitOrder(const OrderRequest& request)
{
    if (!request.IsValid())
    {
        return;
    }

    if (!CanSubmit(request))
    {
        return;
    }

    Submit(request);
}
```

### 7.2. `switch` 문 규칙

- `switch` 문에는 언제나 `default`를 작성한다.
- 처리할 수 없는 값은 `default`에서 즉시 실패가 드러나게 한다.
- 의도적인 fallthrough는 명시적으로 표시한다.
- 여러 `case`가 같은 동작을 수행하면 빈 `case`를 연속해서 배치한다.

좋은 예:

```cpp
switch (status)
{
case EOrderStatus::PENDING:
    Prepare(order);
    break;

case EOrderStatus::PAID:
    Ship(order);
    break;

case EOrderStatus::CANCELED:
    break;

default:
    Assert(false);
    break;
}
```

의도적인 fallthrough가 필요한 경우:

```cpp
switch (status)
{
case EOrderStatus::PENDING:
    Prepare(order);
    [[fallthrough]];

case EOrderStatus::READY:
    Submit(order);
    break;

default:
    Assert(false);
    break;
}
```

### 7.3. 반복문 규칙

- 범위 기반 `for` 문을 우선한다.
- 인덱스가 필요한 경우에만 전통적인 `for` 문을 사용한다.
- 반복 중 컨테이너를 수정할 때는 반복자(iterator) 무효화 규칙을 명확히 확인한다.
- 반복문 안에서 복잡한 조건이 반복되면 의미 있는 함수로 분리한다.

좋은 예:

```cpp
for (const Order& order : orders)
{
    SubmitOrder(order);
}

for (std::size_t index = 0; index < orders.size(); ++index)
{
    UpdateOrderPosition(orders[index], index);
}
```

### 7.4. 람다 사용 규칙

- 람다는 짧고 지역적인 동작에만 사용한다.
- 캡처 목록은 명시적으로 작성한다.
- 긴 람다나 여러 곳에서 재사용되는 람다는 함수로 분리한다.
- 캡처한 객체의 수명이 람다보다 짧아질 수 있으면 람다를 저장하지 않는다.

좋은 예:

```cpp
const std::uint32_t minimumTotalPriceInCents = pricingPolicy.GetHighValueThresholdInCents();

const auto isHighValueOrder = [minimumTotalPriceInCents](const Order& order)
{
    return order.GetTotalPriceInCents() >= minimumTotalPriceInCents;
};

const bool hasHighValueOrder = std::any_of(orders.begin(), orders.end(), isHighValueOrder);
```

### 7.5. `goto` 사용 규칙

- `goto`는 사용하지 않는다.
- 낮은 수준의 정리 코드처럼 불가피한 경우에는 함수 범위를 작게 유지한다.
- `goto` 레이블은 모두 대문자와 밑줄을 사용한다.

## 8. `assert` 사용 규칙

- 외부 입력 검증에는 `assert`를 사용하지 않는다.
- 내부 사전 조건, 후행 조건, 도달할 수 없는 분기에는 `assert`를 사용한다.
- 프로젝트에 전용 `Assert` 함수나 매크로가 있으면 표준 `assert` 대신 해당 기능을 사용한다.
- 컴파일 시간에 확인할 수 있는 조건에는 `static_assert`를 사용한다.
- `assert` 안의 표현식에 부수 효과를 넣지 않는다.

좋은 예:

```cpp
void OrderService::SubmitOrder(OrderId orderId)
{
    Assert(orderId.IsValid());

    mOrderRepository.Submit(orderId);
}

constexpr std::size_t ORDER_HEADER_BYTE_SIZE = 32;
static_assert(sizeof(OrderHeader) == ORDER_HEADER_BYTE_SIZE);
```

## 9. 포인터, 참조, 소유권, 메모리 규칙

### 9.1. 참조와 포인터 선택 규칙

- `nullptr`이 허용되지 않는 필수 객체는 참조로 받는다.
- `nullptr`이 의미 있는 값이면 포인터로 받는다.
- 읽기 전용 참조 매개 변수는 `const T&`로 받는다.
- 읽기 전용 포인터 매개 변수는 포인터와 대상 중 무엇이 바뀌지 않는지 `const`로 드러낸다.
- 출력 매개 변수는 포인터를 사용하고, 이름 앞에 `out`을 붙인다.
- 출력 매개 변수 포인터는 `nullptr`을 허용하지 않으며, 함수 시작 지점에서 확인한다.

좋지 않은 예:

```cpp
void SubmitOrder(OrderRepository* repository, OrderId orderId)
{
    repository->Submit(orderId);
}
```

좋은 예:

```cpp
void SubmitOrder(OrderRepository& repository, OrderId orderId)
{
    repository.Submit(orderId);
}
```

출력 매개 변수 선언 예:

```cpp
bool PacketReader::TryReadPayload(PacketBuffer* outPayloadBuffer, std::size_t* outByteCount);
```

### 9.2. `nullptr` 처리 규칙

- 널 포인터는 `NULL`이나 `0`이 아니라 `nullptr`로 표현한다.
- `nullptr`을 정상 값으로 허용하는 API는 이름이나 문서에 그 의미를 드러낸다.
- 내부 함수의 필수 포인터가 `nullptr`이면 즉시 실패가 드러나게 한다.
- 컨테이너는 가능한 한 널 포인터 대신 빈 컨테이너로 표현한다.

좋지 않은 예:

```cpp
if (order == NULL)
{
    return;
}

std::vector<Order>* GetOrders();
```

좋은 예:

```cpp
if (order == nullptr)
{
    return;
}

std::vector<Order> GetOrders();
```

### 9.3. 소유권 표현 규칙

- 객체 소유권은 코드에서 확인할 수 있어야 한다.
- 단독 소유는 `std::unique_ptr`로 표현한다.
- 공유 소유는 설계상 필요한 경우에만 `std::shared_ptr`로 표현한다.
- 소유하지 않는 필수 객체는 참조로 표현한다.
- 소유하지 않는 선택 객체는 원시 포인터로 표현한다.
- 원시 포인터만 보고 소유권을 추측해야 하는 API를 만들지 않는다.

좋지 않은 예:

```cpp
class OrderService
{
public:
    void SetRepository(OrderRepository* repository);

private:
    OrderRepository* mRepository;
};
```

좋은 예:

```cpp
class OrderService
{
public:
    explicit OrderService(OrderRepository& repository);

private:
    OrderRepository& mRepository;
};
```

소유권을 이전하는 경우:

```cpp
class OrderProcessor
{
public:
    explicit OrderProcessor(std::unique_ptr<OrderRepository> repository);

private:
    std::unique_ptr<OrderRepository> mRepository;
};
```

### 9.4. 이동과 반환값 최적화 규칙

- 지역 객체를 값으로 반환할 때는 `NRVO`를 신뢰한다.
- 지역 객체를 반환할 때 불필요하게 `std::move`를 사용하지 않는다.
- 값의 이동 의도가 분명할 때만 `std::move`를 사용한다.
- 이동 생성자와 이동 대입 연산자는 소유권 타입에서 의미가 분명할 때만 직접 구현한다.

좋지 않은 예:

```cpp
Order CreateOrder()
{
    Order order;
    return std::move(order);
}
```

좋은 예:

```cpp
Order CreateOrder()
{
    Order order;
    return order;
}
```

소유권을 이전하는 경우:

```cpp
void OrderProcessor::SetRepository(std::unique_ptr<OrderRepository> repository)
{
    mRepository = std::move(repository);
}
```

### 9.5. 동적 할당 규칙

- 객체 수명을 원시 포인터의 `new`와 `delete`로 직접 관리하지 않는다.
- 동적 할당이 필요하면 단독 소유를 기본으로 하고, `std::make_unique`를 우선 사용한다.
- 공유 소유가 설계상 필요한 경우에만 `std::shared_ptr`로 표현하고, 생성할 때는 `std::make_shared`를 우선 사용한다.
- 배열이나 버퍼는 가능한 한 `std::array`, `std::vector`, `std::string` 같은 표준 타입으로 표현한다.
- 메모리 블록 연산은 원칙적으로 `trivially copyable` 타입에만 사용한다.

좋지 않은 예:

```cpp
OrderRepository* repository = new OrderRepository();
delete repository;

Order* orders = new Order[count];
delete[] orders;
```

좋은 예:

```cpp
std::vector<std::byte> buffer(size);
std::vector<Order> orders(count);
std::unique_ptr<OrderRepository> repository = std::make_unique<OrderRepository>();
```

## 10. `auto` 사용 규칙

- `auto`는 타입이 오른쪽 표현식에서 분명할 때 사용한다.
- 반복자(iterator)처럼 타입이 길고 의미보다 구현 세부 사항에 가까운 경우에 사용한다.
- 숫자, 문자열, 소유권 타입처럼 타입 자체가 의미를 전달하는 경우에는 명시적으로 쓴다.
- `auto` 때문에 복사, 참조, 소유권 이동 여부가 흐려지면 사용하지 않는다.

좋지 않은 예:

```cpp
auto timeoutSeconds = 30;
auto repository = CreateRepository();
```

좋은 예:

```cpp
const int timeoutSeconds = 30;
std::unique_ptr<OrderRepository> repository = CreateRepository();

const auto it = orders.find(orderId);
```

## 11. 포맷팅 규칙

### 11.1. 들여쓰기 규칙

- 탭(tab)은 Visual Studio 기본값을 사용한다.
- Visual Studio를 사용하지 않을 때는 공백 4칸을 들여쓰기 기준으로 사용한다.
- 탭과 공백을 같은 파일에서 섞지 않는다.

### 11.2. 중괄호 사용 규칙

- 여는 중괄호는 새 줄에 작성한다.
- 한 줄짜리 제어문에도 중괄호를 사용한다.
- `else`, `catch`는 닫는 중괄호 다음 줄에 작성한다.

좋은 예:

```cpp
if (order.IsPaid())
{
    Submit(order);
}
else
{
    Cancel(order);
}
```

### 11.3. 포인터와 참조 표기 규칙

- 포인터와 참조 기호는 타입에 붙인다.
- 포인터와 참조를 한 줄에서 섞어 선언하지 않는다.

좋은 예:

```cpp
void SaveOrder(const OrderRepository& repository, const Order& order, Order* previousOrderOrNull);
```

### 11.4. 선언 규칙

- 한 줄에는 하나의 변수만 선언한다.
- 관련이 없는 선언을 한 줄에 묶지 않는다.
- 함수 선언의 반환 타입, 함수 이름, 매개 변수 목록은 한 줄에 읽히도록 작성한다.
- 매개 변수가 길면 한 줄에 하나씩 나누고, 닫는 괄호와 세미콜론은 새 줄에 둔다.

좋은 예:

```cpp
const int retryCount = order.GetRetryCount();
const int timeoutSeconds = request.GetTimeoutSeconds();

const std::string name = user.GetName();
const std::string description = product.GetDescription();

void SubmitOrder(OrderId orderId, PaymentId paymentId);

void CreateOrder(UserId userId, AddressId addressId, PaymentMethodId paymentMethodId);
```

### 11.5. 초기화 목록 규칙

- 생성자 초기화 목록은 한 멤버당 한 줄로 작성한다.
- 콜론은 생성자 선언 다음 줄에 둔다.
- 각 항목 앞에 쉼표를 두어 항목 추가와 제거가 잘 드러나게 한다.
- 초기화 순서는 멤버 선언 순서와 일치시킨다.

좋은 예:

```cpp
OrderService::OrderService(OrderRepository& repository, PaymentGateway& paymentGateway)
    : mRepository(repository)
    , mPaymentGateway(paymentGateway)
{
}
```

### 11.6. 리터럴 표기 규칙

- `float` 리터럴에는 `f` 접미사를 붙인다.
- 큰 숫자는 읽기 쉽도록 자리 구분자를 사용한다.
- 매직 넘버를 직접 쓰지 말고 의미 있는 상수로 분리한다.

좋은 예:

```cpp
constexpr float DEFAULT_MOVEMENT_SPEED = 1.5f;
constexpr int DEFAULT_TIMEOUT_MILLISECONDS = 30'000;
```

## 12. 주석 작성 규칙

### 12.1. 주석 작성 원칙

- 주석은 코드가 무엇을 하는지 반복하지 않는다.
- 코드만으로 드러나지 않는 이유, 제약, 특별한 판단, 외부 시스템의 특성을 설명한다.
- 공개 API에는 호출자가 알아야 하는 계약이 있을 때만 문서 주석을 작성한다.
- 문서 주석에는 매개 변수의 의미와 단위, 수명 조건, `nullptr` 허용 여부, 반환값, 실패 처리 방식, 호출 순서, 스레드 안전성처럼 호출자가 알아야 하는 계약을 적는다.
- 임시 주석, 죽은 코드, 오래된 TODO는 남기지 않는다.
- TODO에는 담당자나 추적 가능한 이슈 번호를 함께 적는다.

좋지 않은 예:

```cpp
// Checks whether the payment attempt was submitted recently.
if (paymentAttempt.IsRecentlySubmitted(clock.Now()))
{
    return PaymentResult::DuplicateRequest();
}
```

좋은 예:

```cpp
// Payment gateway rejects duplicate approval requests within 30 seconds.
if (paymentAttempt.IsRecentlySubmitted(clock.Now()))
{
    return PaymentResult::DuplicateRequest();
}
```

TODO를 남겨야 하는 경우:

```cpp
// TODO(ORDER-482): Remove fallback after the legacy payment gateway is retired.
const PaymentResult paymentResult = paymentGateway.ApproveOrFallback(paymentRequest);
```

공개 API의 호출 계약을 명시해야 하는 경우:

```cpp
/**
 * Finds an active order by ID.
 *
 * Precondition: `orderId` has already been validated by the caller.
 * Returns: Pointer to the active order owned by the repository, or `nullptr` when the order exists but is not active.
 * Lifetime: The returned pointer remains valid until the repository is modified.
 */
const Order* FindActiveOrderOrNull(OrderId orderId) const;
```

### 12.2. 인라인 주석 규칙

- 인라인 주석은 줄 끝에 길게 붙이지 않는다.
- 코드 위에 독립된 줄로 작성한다.

좋지 않은 예:

```cpp
constexpr int PAYMENT_GATEWAY_DUPLICATE_STATUS_CODE = 409;

if (paymentResponse.StatusCode == PAYMENT_GATEWAY_DUPLICATE_STATUS_CODE) // Payment gateway uses 409 for duplicate approval requests.
{
    return PaymentResult::DuplicateRequest();
}
```

좋은 예:

```cpp
constexpr int PAYMENT_GATEWAY_DUPLICATE_STATUS_CODE = 409;

// Payment gateway uses 409 for duplicate approval requests.
if (paymentResponse.StatusCode == PAYMENT_GATEWAY_DUPLICATE_STATUS_CODE)
{
    return PaymentResult::DuplicateRequest();
}
```

### 12.3. 주석보다 이름을 우선하는 규칙

- 변수, 함수, 타입의 의미를 설명하기 위한 주석이 필요하다면 이름을 먼저 고친다.

좋지 않은 예:

```cpp
// Checks whether the user can cancel the order.
bool Check(User user, Order order);
```

좋은 예:

```cpp
bool CanUserCancelOrder(const User& user, const Order& order);
```
