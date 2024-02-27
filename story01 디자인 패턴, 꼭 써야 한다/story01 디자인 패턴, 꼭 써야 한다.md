# java-performance-tuning


- 관련 링크
    
    [Core J2EE Patterns, 자바 핵심 디자인 패턴](https://modimodi.tistory.com/m/66)
    
    [Service Locator Pattern, 서비스 로케이터 패턴](https://blog.mayleaf.dev/25)
    
    [DI(Dependency Injection)와 서비스 로케이터](https://velog.io/@tco0427/DIDependency-Injection와-서비스-로케이터)
    

---

# J2EE 패턴

- J2EE 패턴에는 MVC 구조가 기본으로 깔려 있음

### MVC 모델

- 화면에 모든 처리 로직을 모아 두는 게 아닌 Model, View, Controller  역할을 하는 클래스를 각각 만들어서 개발하는 모델
- View
    - 사용자가 결과 보거나 입력할 수 있는 화면
    - 이벤트 발생(버튼 누르기)시키고, 이벤트의 결과를 보여주는 역할
- Controller
    - View와 Model의 연결자
    - View에서 받은 이벤트를 모델로 연결하는 역할
- Model
    - View에서 입력된 내용을 저장, 관리, 수정하는 역할

### J2EE 디자인 패턴이란

- pattern 이란?
    - 시스템을 만들기 위해 전체 중 일부 의미 있는 클래스들을 묶은 각각의 집합을 디자인 패턴이라고 함
- J2EE는 Java 2 Platform Enterprise Edition의 줄임말로 JAVA SE를 확장한 엔터프라이즈 사양 세트를 의미
- 반복되는 설계 문제를 해결하기 위한 모범 사례 모음
- J2EE 디자인 패턴은 여러 종류 존재 → 크게 3개 layer로 나눠짐
    - 위로 갈수록 화면에 가깝고, 아래로 갈수록 DB(저장소)에 가까움

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/41fbaedf-6ea6-42cb-80d2-9dd665057db8/e92135ee-1e6a-47c4-a98f-8e214ec9ab05/Untitled.png)

### 1. Presentation tier

- Intercepting Filter
    - **Request, Response의 전/후 처리를 하고자 할 때 사용**
    - 실제 대상 응용 프로그램에 요청 전달하기 전에 필터가 정의되고 요청에 적용된다
    - 주로 필터에서 인증/승인/로그인 작업 등을 수행한 다음 해당 처리기에 요청을 전송
    - Servlet Filter, Spring Interceptor가 이 패턴을 기반으로 함
        
        [7. 로그인 처리2 - 필터, 인터셉터.pdf](https://prod-files-secure.s3.us-west-2.amazonaws.com/41fbaedf-6ea6-42cb-80d2-9dd665057db8/20ffd723-1f31-4fc3-b87a-c8c7a420b18a/7._%EB%A1%9C%EA%B7%B8%EC%9D%B8_%EC%B2%98%EB%A6%AC2_-_%ED%95%84%ED%84%B0_%EC%9D%B8%ED%84%B0%EC%85%89%ED%84%B0.pdf)
        
    - **Servlet Filter**
        - Java Servlet Specification의 일부로, 모든 요청과 응답을 가로채는 데 사용
        - 주로 인증, 로깅, 요청/응답 변환 등의 목적으로 사용
        - 흐름 : HTTP 요청 → WAS → 필터 → 서블릿 → 컨트롤러
            - 필터에서 적절하지 않은 요청이라 판단 → 서블릿 호출하지 않고 끝냄 → 그래서 로그인 여부 체크하기에 필터가 좋음
            - 필터를 여러 개 추가할 수 있음 = 체인
                - HTTP 요청 → WAS -> 필터1 → 필터2 → 필터3 → 서블릿 → 컨트롤러
        - Spring 애플리케이션 내외부의 HTTP 요청/응답에 대해 동작
    - Spring Interceptor 호출 흐름
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/41fbaedf-6ea6-42cb-80d2-9dd665057db8/5921e209-dfef-453c-881e-e4058bf7d8a2/Untitled.png)
        
- Front Controller
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/41fbaedf-6ea6-42cb-80d2-9dd665057db8/7cc5c0f9-b8ea-451f-bdba-8bbd5a1ff5a2/Untitled.png)
    
    - 프론트 컨트롤러(디자인 패턴 이름) = 디스패처 서블릿(프론트 컨트롤러 패턴의 구현체)
        - 제일 앞단에 다양한 요청을 제어하는 Controller를 두어 단일 진입하게 함
            - 즉, client(선생)의 요청들을 프론트 컨트롤러(반장)가 다 관리
        - HTTP로 들어오는 모든 요청을 가장 먼저 받아 적합한 컨트롤러에 위임
        - view(학생) 관리에도 용이하고 서비스(데이터 제공받는 서비스 계층)를 관리하기 좋다
    
    ### 설명
    
    1. client로부터 어떠한 요청이 오면 Tomcat과 같은 서블릿 컨테이너가 요청을 받음
    
    1. 이 모든 요청을 프론트 컨트롤러인 디스패처 서블릿이 가장 먼저 받음
    2. 디스패처 서블릿은 공통 작업 먼저 처리 후, 해당 요청 처리할 컨트롤러 찾아 작업을 위임
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/41fbaedf-6ea6-42cb-80d2-9dd665057db8/141c337f-5f5a-4f6e-9070-74ae1b54bd49/Untitled.png)
    
- View Helper
    - View를 구성할 때 사용되는 패턴
    - View 내에서 직접 비지니스 로직을 처리하지 않고, 이런 로직들을 Helper 클래스로 분리해 처리함 → View는 오직 사용자에게 보여주는 역할에만 집중 가능
    - Helper 클래스
        - View에서 필요한 데이터를 준비하고 비지니스 로직을 실행하는 클래스
    - View
        - JSP, Thymeleaf 등
        - Helper 클래스 사용해 필요한 데이터를 처리하고 표시함
    - 예시
        
        ```java
        public class UserListHelper {
            public List<User> getUsers() {
                // 비즈니스 로직을 사용하여 사용자 목록을 가져옴
                UserService userService = new UserService();
                return userService.findAllUsers();
            }
        }
        ```
        
        ```html
        <%@ page import="com.example.UserListHelper" %>
        <%@ page import="com.example.User" %>
        <%
        UserListHelper userListHelper = new UserListHelper();
        List<User> users = userListHelper.getUsers();
        %>
        <html>
        <body>
            <h2>User List</h2>
            <ul>
                <% for(User user : users) { %>
                    <li><%= user.getName() %></li>
                <% } %>
            </ul>
        </body>
        </html>
        
        ```
        
        - JSP 파일에서는 이 Helper 클래스를 사용하여 사용자 목록을 쉽게 가져올 수 있음
        
    
    ⇒ 이게 아까 설명한 J2EE 패턴이 MCV 패턴 기반이라고 한 것의 사례
    
- Composite View (합성 뷰)

### 2. Business tier

- Business Delegate
    - Presentation tier와 Business tier 사이의 결합도를 줄이기 위해 사용
    - 두 계층 사이의 중재자 역할
    - 작동 방식
        1. 클라이언트(예: JSP, Servlet)는 Business Delegate를 통해 비즈니스 로직을 실행하고자 함
        2. Business Delegate는 필요한 경우 Lookup Service를 사용하여 비즈니스 서비스의 인스턴스를 찾음
        3. Business Delegate는 비즈니스 서비스를 호출하고, 비즈니스 로직을 실행. 이 과정에서 네트워크 에러나 서비스 불가능 상태 등의 예외 처리도 적절히 관리할 수 있음
        4. 비즈니스 서비스로부터 결과를 받은 Business Delegate는 이를 클라이언트에 반환
    - 예시 코드
        
        ```java
        public class UserBusinessDelegate {
            private UserService lookupService = new UserService();
        
            public User getUserDetails(String userId) {
                // UserService를 사용하여 실제 비즈니스 서비스의 인스턴스를 얻음
                User user = lookupService.findUserById(userId);
                return user;
            }
        }
        ```
        
    - 클라이언트는 Business Delegate를 통해 간접적으로 비즈니스 서비스에 접근하므로, 비즈니스 서비스의 구현 방식이 변경되어도 클라이언트 코드를 수정할 필요가 없음
- Service Locator
    - 서비스와 컴포넌트 검색을 쉽게 하는 패턴
    - 서비스 로케이터 `스스로 어떤 객체를 제공해야할지에 대해서 알고 있어야 한다.`
    - 클라이언트가 서비스를 직접 찾는 대신, 중앙 집중식 레지스트리를 통해 필요한 서비스를 조회하고 접근할 수 있도록 하는 것
    - JNDI 조회와 같은 비용이 많이 드는 리소스를 캐싱하여, 애플리케이션의 성능을 향상시키는 역할 → 그래서 저자가 J2EE 패턴 중 성능과 가장 밀접한 패턴이라고 한 것
        - JNDI 조회가 왜 비용이 많이 들까?
            - 네트워크 통신 : 원격 서버에 저장된 리소스를 찾기 위해 네트워크 호출
            - 요청 받은 리소스 이름에 대응하는 실제 리소스 찾기 위해 디렉토리 서비스 조회
    - 예시 코드
        
        ```java
        package story01;
        
        import java.util.*;
        import javax.naming.*;
        import java.rmi.*;
        import javax.ejb.*;
        public class ServiceLocator {
            private InitialContext ic;
            private Map cache;
            private static ServiceLocator me;
            static {
                me = new ServiceLocator();
            }
        
            private ServiceLocator() {
                cache = Collections.synchronizedMap(new HashMap());
            }
        
            public InitialContext getInitialContext() throws Exception {
                try {
                    if (ic == null) {
                        ic = new InitialContext();
                    }
                } catch (Exception e) {
                    throw e;
                }
                return ic;
            }
        
            public static ServiceLocator getInstance() {
                return me;
            }
        }
        
        ```
        
        - Static Block을 활용해서 싱글톤 객체 유지
        - Cache : 이미 조회된 서비스 인스턴스를 저장 → 반복된 서비스 조회 요청에 대한 성능을 향상
        - **Initial Context :** JNDI 조회와 같은 외부 서비스 검색 메커니즘에 대한 접근을 제공
- Business Delegate & Service Locator 예시 코드
    
    ```java
    public class UserBusinessDelegate {
        private ServiceLocator serviceLocator;
        private UserService lookupService;
    
        public UserBusinessDelegate() {
            this.serviceLocator = ServiceLocator.getInstance(); // Service Locator 인스턴스를 얻음
            this.lookupService = (UserService) serviceLocator.getService("UserService"); // Service Locator를 통해 UserService 인스턴스를 조회
        }
    
        public User getUserDetails(String userId) {
            // 조회된 UserService를 사용하여 실제 비즈니스 서비스의 인스턴스를 얻음
            User user = lookupService.findUserById(userId);
            return user;
        }
    }
    
    ```
    
    - Presentation은 그냥 BusinessDelegate를 통해서 Service 결과값 받아감
    - BusinessDelegate에서는 ServiceLocator를 활용해 Service를 효율적으로 검색함
- Service Locator 단점 및 대안
    - 단점
        1. 모든 컴포넌트들이 싱글톤 Service Locator에 대한 래퍼런스 가지고 있어야 함
        2. 테스트하기 어려움?
        3. 로케이터를 통해서 필요한 객체를 `직접 찾는 방식`
    - 대표적인 대안
        - Dependency Injection
            - 직접 찾는게 아닌 외부에서 사용할 객체를 주입받는게 더 좋다
            - 스스로 의존하고 있는 객체를 찾거나 생성하지 않고, 외부로부터 주입
- Session Facade
    - 엔터프라이즈 자바 애플리케이션의 비즈니스 계층을 클라이언트에게 단순화된 인터페이스로 제공하는 구조적인 방법
    - 클라이언트와 비즈니스 서비스 간의 복잡한 상호작용을 감추기 위해 사용
    - 예시
        - 고객 정보를 관리하는 시스템에서 여러 서비스(고객 조회, 주문 관리, 결제 처리 등)가 필요한 경우
        - 각각의 서비스를 직접 호출하는 대신 Session Facade를 구현하여 이러한 서비스들을 하나의 인터페이스 뒤에 숨길 수 있음
        - 클라이언트는 Session Facade를 통해 필요한 작업을 요청하고, Facade는 내부적으로 적절한 비즈니스 로직을 호출하여 요청을 처리
        
        ```java
        @Stateless
        public class CustomerFacade {
            @EJB
            private CustomerService customerService;
            @EJB
            private OrderService orderService;
            @EJB
            private PaymentService paymentService;
        
            public CustomerDTO getCustomerDetails(String customerId) {
                // 고객 정보 조회
                Customer customer = customerService.findCustomerById(customerId);
                // 해당 고객의 주문 및 결제 정보도 함께 처리
                List<Order> orders = orderService.findOrdersByCustomerId(customerId);
                PaymentInfo paymentInfo = paymentService.getPaymentInfo(customerId);
                // 필요한 정보를 조합하여 반환
                return new CustomerDTO(customer, orders, paymentInfo);
            }
        }
        
        ```
        
        → 클라이언트에게 단일 진입점을 제공
        
        → 라이언트는 복잡한 서비스 상호작용을 신경 쓰지 않고 필요한 데이터를 효율적으로 조회
        
    - ITAM 코드를 통한 예시
        
        ```java
        public class ContractService {
        
            private final ContractRepository contractRepository;
            private final AssetRepository assetRepository;
            private final SupplyTypeRepository supplyTypeRepository;
            private final PeriodTypeRepository periodTypeRepository;
            private final NumOfUsersTypeRepository numOfUsersTypeRepository;
            private final UserAssetRequestInfoRepository userAssetRequestInfoRepository;
        
            private final AssetRequestService assetRequestService;
            ....
        }
        ```
        
        - 서비스 내 **비즈니스 로직을 캡슐화**하고 있으며, 이를 통해 클라이언트는 복잡한 비즈니스 로직을 직접 다루지 않고 **`ContractService`**를 통해 간접적으로 접근합니다. 이는 Session Facade 패턴 중 하나
        - 원래 Session Facade는 자바 빈즈(EJB) 관련 용어들로 사용, 분산 컴포넌트 간의 복잡성 줄이기 위해 설계되었음
        - 제시된 코드는 Session Facade 패턴의 목적과 원칙을 따르고 있지만, Spring 프레임워크와 같은 현대적인 기술 스택을 사용하여 구현되었습니다. 이는 J2EE 패턴을 현대적인 애플리케이션 아키텍처에 적용한 예
    - 

### 3. Integration tier

- Transfer Object
    - 하나의 객체에 여러 타입의 값을 담아 전달하는 일을 수행
    
    ### 왜 써?
    
    - 설명 그래로 하나의 객체에 결과 값을 담아 올 수 있어서 재활용 가능
        - 여러 번 요청할 필요 없음
        - 네트워크를 통한 원격 요청을 줄일 수 있음
    
    ```java
    package story01;
    
    import java.io.*;
    
    public class EmployeeTO implements Serializable{
    
        private String empName;
        private String empID;
        private String empPhone;
    
        public EmployeeTO() {
            super();
        }
    
        public EmployeeTO(String empName, String empID, String empPhone) {
            super();
            this.empName = empName;
            this.empID = empID;
            this.empPhone = empPhone;
        }
    
        public String getEmpName() {
            if (empName == null) return "";
            return empName;
        }
    
        public void setEmpName(String empName) {
            this.empName = empName;
        }
    
        public String getEmpID() {
            return empID;
        }
    
        public void setEmpID(String empID) {
            this.empID = empID;
        }
    
        public String getEmpPhone() {
            return empPhone;
        }
    
        public void setEmpPhone(String empPhone) {
            this.empPhone = empPhone;
        }
    
        public String toString() {
            StringBuilder sb = new StringBuilder();
            sb.append("empName=").append(empName).append("empID=")
                    .append(empID).append(" empPhone=").append(empPhone);
            return sb.toString();
    
        }
    }
    
    ```
    
    ### TO 패턴에서 toString() 꼭 구현해라
    
    - 구현 안해놓으면 참조 값이 나온다
    - 추후에 테스트 코드 작성 시, 필요함
    
    ### TO의 getter에서 널 체크하면 좋다
    
    - 이 TO를 사용하는 각 소스에서 일일이 null 체크 할 필요가 없어진다
- Data Access Object
    - 데이터베이스나 다른 저장 매체에 접근하는 로직과 비즈니스 로직을 분리하는 디자인 패턴
    
    → 이게 JPA Repository 
    
    - 구성 요소
        - **DAO 인터페이스**: 이 인터페이스는 데이터 액세스를 위한 표준 메소드 집합을 정의합니다. 예를 들어, **`create`**, **`read`**, **`update`**, **`delete`**(CRUD) 작업을 위한 메소드가 포함
        - **DAO 구현 클래스**: DAO 인터페이스를 구현하는 클래스로, 실제 데이터베이스 호출을 처리합니다. 이 구현은 특정 데이터베이스 기술(JDBC, JPA, Hibernate 등)에 의존
    - 작동 방식
        1. 비즈니스 로직은 DAO 인터페이스를 사용하여 데이터를 요청하거나 변경
        2. DAO 구현 클래스는 이러한 요청을 받고, 적절한 데이터베이스 호출을 실행
        3. 결과 데이터는 모델 객체 또는 DTO를 통해 비즈니스 로직으로 반환

---

### 2티어??

- **Presentation**, **Data** Layer
- WEB 서버, DB 서버 → 2티어

!https://blog.kakaocdn.net/dn/bIiS1y/btq7TLV2IUO/WEMhCj2C1F28MimnGNRYY1/img.png

- WEB 서버, WAS 서버, DB 서버 → 3티어
- **웹서버가 DB에 연결되는 형태**
- 트래픽이 많지 않은 경우 자주 사용되는 구조
- 하나의 클라이언트에 서버 프로세스가 하나씩 생성되는 방식

### 3티어??

- **Presentation**, **Application**, **Data** Layer로 구분
- **WEB** 서버, **WAS** 서버, **DB** 서버 (배포 및 인프라 관점에서의 분류)
    - WEB 서버 : 정적인 컨텐트를 클라이언트에게 제공(Presentation의 일부 기능 담당)
    - WAS 서버 : 웹 서버와 함께 동적인 컨텐츠를 처리하기 위해 사용되며, 애플리케이션 레이어의 비즈니스 로직 처리를 담당
        - 데이터 레이어와 통신하여 동적인 데이터를 생성하고, 이를 웹 서버를 통해 사용자에게 전달
    - DB 서버 : 데이터베이스 관리 시스템(DBMS)을 실행하고, 애플리케이션의 데이터를 저장, 관리하는 서버입니다. 데이터 레이어의 기능을 수행
    
    !https://blog.kakaocdn.net/dn/cnZmzQ/btq7UN0btVc/fKgt6jhwDU7onpMSu5UkS0/img.png
    

### 4티어??

- **Presentation**, **Business**, **Persistence**, **Database** Layer
- Persistence Layer = Data Access Layer
    - API를 통해 Database Layer와 통신해 SQL Query문으로 data 가져오고 처리하는 계층
- Spring framework

## 3티어로 되어 있는 JSP 모델은 주로 모델1과 모델2를 사용

### 모델1

- JSP가 직접 비지니스 로직 & 데이터 처리 담당 & 사용자 인터페이스 표현도 처리
- JSP 페이지 내에 HTML 코드 & 자바 코드 혼합 → 유지보수 hell

### 모델2

- MVC 패턴 기반임
- 서블릿이 client에게 요청 받아서 데이터 처리하고 모델에 담아 뷰에게 전달
- JSP 페이지가 뷰 역할

### Front Controller Pattern 링크

[주니어 개발자도 이해하는 코어 J2EE 패턴 - 학급반장편 -](https://www.slideshare.net/hnki0104/j2ee-43049360)

[[Spring] Dispatcher-Servlet(디스패처 서블릿)이란? 디스패처 서블릿의 개념과 동작 과정](https://mangkyu.tistory.com/18#recentEntries)

# Service Locator 패턴

- J2EE 디자인 패턴 중 하나로, 서비스(EJB, JDBC)에 대한 접근을 간소화하고 중앙에서 관리할 수 있도록 해주는 디자인 패턴
- 이 ServiceLocator 인스턴스는 싱글톤 패턴을 사용해 인스턴스가 하나만 생성되도록 한다
    - static block과 private 생성자를 활용해 싱글톤 패턴 구현

```java
package story01;

import java.util.*;
import javax.naming.*;
import java.rmi.*;
import javax.ejb.*;
public class ServiceLocator {
    private InitialContext ic;
    private Map cache;
    private static ServiceLocator me;
    static {
        me = new ServiceLocator();
    }

    private ServiceLocator() {
        cache = Collections.synchronizedMap(new HashMap());
    }
    public InitialContext getInitialContext() throws Exception {
        try {
            if (ic == null) {
                ic = new InitialContext();
            }
        } catch (Exception e) {
            throw e;
        }
        return ic;
    }

    public static ServiceLocator getInstance() {
        return me;
    }
}

```

### **예시 설명**

- 예를 들어, **`MemberService`** 객체에 접근해야 한다고 가정
- **`MemberService`**는 비즈니스 로직을 수행하는 서비스 레이어의 컴포넌트이며, 데이터베이스와 상호작용하는 **`MemberRepository`**를 사용할 수 있음
1. **서비스 조회**: 클라이언트가 **`ServiceLocator`**를 사용하여 **`MemberService`**를 요청합니다.
    - **`ServiceLocator`**는 먼저 캐시를 확인하여 요청된 서비스 객체의 인스턴스가 존재하는지 검사합니다.
    - 캐시에 서비스 객체가 없으면, **`InitialContext`**를 사용하여 JNDI를 통해 서비스 객체를 찾습니다.
    - 찾은 서비스 객체는 캐시에 저장되어, 다음 번 같은 서비스 객체 요청 시 빠르게 반환될 수 있습니다.
2. **서비스 사용**: 클라이언트는 **`ServiceLocator`**로부터 받은 **`MemberService`** 인스턴스를 사용하여 필요한 비즈니스 로직을 실행
    
    ⇒  **`ServiceLocator`**는 “**서비스 객체들에 대한 접근을 간소화”**하고, “**의존성 관리를 중앙에서 수행”**하여 애플리케이션의 유지보수성과 확장성을 높이는 데 도움을 줍니다.
    

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/41fbaedf-6ea6-42cb-80d2-9dd665057db8/f01a7d88-6b55-44a7-943c-71c821ef8395/Untitled.png)

# JNDI가 모야

- Java Naming and Directory Interface
- 자바 API 중 하나
- 다양한 네이밍 및 디렉토리 서비스에 접근하기 위한 인터페이스 제공
    
    → 개발자는 특정 디렉토리 서비스 구현에 종속되지 않고, 일관된 방법으로 리소스를 찾고 접근 가능
    
- 주로 네트워크 상의 객체를 찾기 위해 사용
    - 가장 일반적인 사용 사례 중 하나는 Java EE(Enterprise Edition) 환경에서 **데이터베이스 연결**, **메시징 큐**, **메일 세션**과 같은 **엔터프라이즈 리소스를 찾고 접근**

### 주요 기능

1. Naming Service 
    - 객체를 이름으로 찾을 수 있게 해주는 서비스
    - 예를 들어, 데이터베이스 연결을 위한 DataSource 객체에 대한 참조를 얻기 위해 사용자는 JNDI를 사용하여 해당 객체의 이름을 조회
2. Directory Service
    - 객체에 대한 상세 정보를 저장하고 조회할 수 있는 서비스
    - LDAP(Lightweight Directory Access Protocol)과 같은 디렉토리 서비스를 사용하여 보다 복잡한 쿼리와 구조화된 정보에 접근할 수 있게 해줌

### 사용 예시

- 개발자는 JNDI를 사용하여 애플리케이션 서버의 리소스를 찾고 접근할 수 있음.
- 예를 들어, 데이터베이스 연결 풀에 접근하기 위해 개발자는 JNDI 이름을 사용하여 **`DataSource`** 객체를 조회할 수 있음
1. **InitialContext** 생성
    - JNDI API를 사용하기 위한 초기 진입점
    - 이 컨텍스트는 네이밍 및 디렉토리 서비스와의 통신을 위한 시작점 역할을 함
2. **리소스 조회**
    - **`InitialContext.lookup()`** 메서드를 사용하여 필요한 리소스의 JNDI 이름을 지정하고 조회
    - 예를 들어, **`DataSource`** 객체를 찾기 위해 **`"java:comp/env/jdbc/MyDataSource"`**와 같은 이름을 사용할 수 있음
3. **리소스 사용**
    - 조회된 객체를 캐스팅하여 실제 필요한 작업을 수행
    - 예를 들어, **`DataSource`** 객체를 사용하여 데이터베이스 연결을 얻고 SQL 쿼리를 실행할 수 있음
    
    ⇒ JNDI는 이와 같이 다양한 리소스와 서비스에 대한 통합된 접근 방식을 제공하여, Java EE 환경에서의 애플리케이션 개발을 보다 유연하고 관리하기 쉽게 만들어 줌
    

