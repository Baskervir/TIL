*** 예외 처리 ***
    - 프로그램 오류 : 어떤 원인에 의해 오작동 또는 비정상적 종료되는 경우, 이 결과를 초래하는 원인

[발생 시점에 따른 오류]
    - 런타임 오류 : 프로그램의 실행도중에 발생하는 에러
    - 컴파일 오류 : 컴파일 할 때 발생하는 에러
    - 논리적 에러 : 컴파일과 실행이 잘 되지만, 의도와 다르게 동작하는 경우

컴파일러가 소스코드(*.java)에 대해 오타 및 잘못된 구문, 자료형 체크 등의 검사 수행 > 오류 발생시 컴파일 오류
    > 컴파일 성공
    > 클래스 파일(*.class)이 생성
    > 생성된 클래스 파일 실행 가능
    [단, 실행도중에 발생할 수 있는 잠재적인 오류 검사 불가]

    개발자는 런타임 에러를 방지하기 위해 발생 가능한 오류에 대한 경우의 수를 고려하여 대비를 해야한다

[에러]와 [예외] 두가지로 구분한다
    - 에러 : 프로그램 코드에 의해서 수습될 수 없는 심각한 오류
    - 예외 : 프로그램 코드에 의해서 수습될 수 있는 다소 미약한 오류

*** 예외 클래스의 계층 구조 ***
    실행 시 발생 가능한 오류(Exception & Error)를 클래스로 정의
    [모든 클래스의 조상은 Object클래스] 이므로 Exception과 Error클래스 역시 Object클래스의 자손이다
    모든 예외의 최고 조상은 Exception클래스
        [Exception]
        ㄴ [IOException]
        ㄴ [ClassNotFoundException]
        ㄴ [...]
        ㄴ [RuntimeException]
            ㄴ [ArithmeticException]
            ㄴ [ClassCastException]
            ㄴ [NullPointerException]
            ㄴ [...]
            ㄴ [IndexOutOfBoundsException]
    RuntimeException클래스들 : 프로그래머의 실수에 의해서 발생할 수 있는 예외들
    Exception클래스들 : 사용자 및  외부의 영향으로 발생할 수 있는 예외들

    발생한 예외를 처리하지 못하면, 프로그램은 비정상적으로 종료되며, 처리되지 못한 예외(uncaught exception)는
    JVM의 '예외처리기(UncaughtExceptionHandler)'가 받아서 예외의 원인을 화면에 출력한다

*** 예외처리하기 - try-catch문 ***
    try {
        // 예외가 발생할 가능성이 있는 문장들
    } catch (Exception e1) {
        // [Exception1 발생] > 이를 처리하기 위한 문장
    } catch (Exception e2) {
        // [Exception2 발생] > 이를 처리하기 위한 문장
    } catch (Exception eN) {
        // [ExceptionN 발생] > 이를 처리하기 위한 문장
    }

    하나 이상의 catch블럭이 올 수 있으나, 이 중 발생한 예외의 종류와 일치하는 단 한개의 catch블럭만 수행된다
    catch블럭이 존재하지 않으면, 예외는 처리되지 않는다
    try블럭 또는 catch블럭에 또 다른 try-catch문이 포함될 수 있다
    내부 try-catch문일 경우, 변수 e를 중복해서 선언해서는 안된다

*** try-catch문에서의 흐름 ***
    [try블럭 내에서 예외가 발생한 경우]
        1. 발생한 예외와 일치하는 catch블럭이 있는지 확인
        2. 일치하는 catch블럭을 찾게 되면, 그 catch블럭 내의 문장들을 수행하고 전체 try-catch문을 빠져나간다
        > 다음 문장을 계속해서 수행
        > 만약 일치하는 cathc블럭을 찾지 못하면, 예외는 처리되지 않는다
    [try블럭 내에서 예외가 발생하지 않은 경우]
        1. catch블럭을 거치지 않고 전체 try-catch문을 빠져나가서 수행을 계속한다

    try문 수행도중 예외 발생하면, 이후 문장은 실행되지 않는다
    try문 내부에 예외가 없을 경우, catch문 내부의 코드들은 무시된다

*** 예외 발생과 catch블럭 ***
    catch블럭의 괄호()내에는 처리하고자 하는 예외와 같은 타입의 참조변수를 선언해야 한다
    예외가 발생하면, 발생한 예외에 해당하는 클래스의 인스턴스가 만들어진다
    예외가 발생한 문장이 try블럭에 포함되어 있다면, 이 예외를 처리할 수 있는 catch블럭이 있는지 찾는다
    처음 catch블럭부터 차례대로 catch블럭의 괄호()내에 선언된 참조변수의 종류와 생성된 예외클래스의 인스턴스에 instanceof연산자를 이용
    > 검사
    > true인 catch블럭을 만날 때까지 검사는 계속된다    // true인 catch블럭을 만나면 이후 catch블럭은 실행되지 않는다
    > true인 catch블럭을 찾게 되면 블럭에 있는 문장들을 모두 수행    // true인 catch블럭이 하나도 없으면 예외는 처리되지 않는다
    > try-catch문을 빠져나가고 예외는 처리된다

    Exception클래스는 모든 예외 클래스들의 조상
    Exception클래스 타입이 참조변수라면, 모든 종류의 예외를 catch블럭에 의해 처리 가능
    Exception은 마지막 보루! 평소엔 예외를 구체적으로 나누어 유지보수 및 디버깅을 용이하게 한다

[실무에서의 예외처리 흐름]
    try {
        ...
    } catch (SpecificException e) {
        ...
    } catch (AnotherSpecificException e) {
        ...
    } catch (Exception e) {
        // 마지막 catch - 못 잡은 거 여기서 처리
    }

[printStackTrace()]
    예외발생 당시의 호출스택(Call Stack)에 있었던 메서드의 정보와 예외 메세지를 화면에 출력한다
[getMessage()]
    발생한 예외 클래스의 인스턴스에 저장된 메시지를 얻을 수 있다

*** 멀티 catch블럭 ***
    여러 catch블럭을 '|'기호를 이용해 하나의 catch블럭으로 합칠 수 있으며, 중복된 코드를 줄일 수 있다
    try {                                          try {
        ...                               \            ...
    } catch (ExceptionA e) {         ===== \       } catch (ExceptionA | ExceptionB e) {
        e.pringStackTrace();         ===== /           e.printStackTrace();
    } catch (ExceptionB e2) {             /        }
        e2.printStackTrace();
    }

    멀티 catch블럭의 '|'기호로 연결된 예외 클래스가 조상과 자손의 관계에 있다면 컴파일 에러가 발생한다
    발생한 예외를 멀티 catch블럭으로 처리하게 될 경우 > 실제로 발생한 예외를 알 수 없다

*** 예외 발생시키기 ***
    키워드 throw를 사용해서 예외를 발생시킬 수 있다
    1. 연산자 new를 이용해서 발생시키려는 예외 클래스의 객체를 만든다
        Exception e = new Exception("예외 발생시키기");
    2. 키워드 throw를 이용해서 예외를 발생시킨다
        throw e;

    throw new Exception("예외 발생시키기");
    생성자에 String을 넣어 주면, 이 String이 Exception 인스턴스에 메시지로 저장된다
    메시지는 getMessage()를 이용해서 얻을 수 있다

[unchecked 예외]
    컴파일러가 확인하지 않는 RuntimeException클래스들
[checked 예외]
    컴파일러가 확인하는 Exception클래스들

*** 메서드에 예외 선언하기 ***
    메서드에 예외를 선언하려면, 메서드의 선언부에 키워드 throws를 사용
    > 메서드 내에서 발생할 수 있는 예외를 명시해준다
    > 예외가 여러 개일 경우 쉼표(,)로 구분한다

    Exception클래스를 메서드에 선언하면, 모든 종류의 예외가 발생할 가능성이 있다는 뜻이다
    이 경우, 이 예외뿐만 아니라 그 자손타입의 예외까지도 발생할 수 있다
    메서드의 선언부에 예외를 선언함으로써 메서드를 사용하려는 사람이 처리되야 할 예외들을 쉽게 알 수 있다
    메서드에 예외를 선언할 때 일반적으로 RuntimeException클래스들은 적지 않는다
        > 보통 반드시 처리해주어야 하는 예외들만 선언한다    // Exception클래스들

    예외를 메서드의 throws에 명시하는 것
    > 예외 처리가 아니다!
    > 자신(예외가 발생할 가능성이 있는 메서드)을 호출한 메서드에게 예외를 전달하여 예외처리를 떠맡기는 것!!!

    예외를 전달받은 메서드가 또도시 자신을 호출한 메서드에게 전달할 수 있으며, 호출스택에 있는 메서드들을 따라 전달
    > 제일 마지막에 있는 main메서드에서도 예외가 처리되지 않으면, main메서드마저 종료되어 프로그램이 종료된다

[예외가 상위 메서드로 전달되지 않기 위해서는 해당 클래스에서 try-catch문으로 예외를 처리해줘야 한다], [catch문 내부가 예외를 처리!]

*** finally블럭 ***
    예외의 발생여부에 상관없이 실행되어야할 코드를 포함시킬 목적으로 사용된다
    try-catch문의 끝에 선택적으로 덧붙여 사용한다
    try -> catch -> finally

    프로그램을 설치하는 작업 환경을 가정해보자
    설치를 위한 파일들의 복사본을 만든다
    설치 도중 오류 발생시 예외를 처리해 복사본들은 삭제되어야 한다
    오류 없이 설치가 완료되더라도 복사본의 필요성은 없어진다
    이런 상황에서 예외 발생 여부와 상관없이 반드시 실행되어야 하는 로직으로서 복사본 삭제는 수행되어야 한다

*** 자동 자원 반환 - try-with-resources문 ***
    입출력(I/O)과 관련된 클래스를 사용할때 유용하다
    입출력에 사용되는 클래스 중에서는 사용한 후에 반드시 닫아 줘야 하는 것들이 있다 > 사용했던 자원의 반환
    
    예외 발생 여부와 상관없이 자원 반환은 이루어져야 하기에 finally블럭에 작성
    
*** 사용자정의 예외 만들기 ***
    기존의 정의된 예외 클래스 외에 필요에 따라 새로운 예외 클래스 정의 가능
    보통 Exception클래스 또는 RuntimeException클래스로부터 상속받아 만든다 > 필요에 따라 선택 가능
    정의된 예외 클래스에는 필요하다면, 벰버변수나 메서드를 추가할 수 있다
    생성 시에 String값을 받아서 메시지로 저장할 수 있다 // String을 매개변수로 받는 생성자 추가해야 한다
    
    필요에 따라 예외처리의 여부를 선택할 수 있는 'unchecked 예외'가 강제적인 'checked 예외'보다 더 선호된다

*** 예외 되던지기 ***
    하위 계층에서 예외를 부분적으로 처리하거나 로그만 남기고, 다시 상위 계층으로 예외를 던지는 것
    한 메서드에서 발생할 수 있는 예외가 여럿인 경우
    > 몇 개는 try-catch문을 통해서 메서드 내에서 자체적으로 처리
    > 나머지는 선언부에 지정하여 호출한 메서드에서 처리
    > 단 하나의 예외에 대해서도 예외가 발생한 메서드와 호출한 메서드, 양쪽에서 처리하도록 할 수 있다
    
    이는 예외를 처리한 후에 인위적으로 다시 발생시키는 방법을 통해 가능  :   예외 되던지기 (exception re-throwing)
    > 예외가 발생할 가능성이 있는 메서드에서 try-catch문을 사용
    > 예외를 처리
    > catch문에서 필요한 작업을 행한다
    > throw문을 사용해서 예외를 다시 발생시킨다
    > 다시 발생한 예외는 이 메서드를 호출한 메서드에게 전달
    > 호출한 메서드의 try-catch문에서 예외를 또다시 처리한다
```declarative
class ExceptionEx {
    public static void main(String[] args) {
        try {
            method1();
        } catch (Exception e) {
            System.out.println("main메서드에서 예외가 처리되었습니다.");
        }
    }
    
    static void method1() throws Exception {
        try {
            throw enw Exception();
        } catch (Exception e) {
            System.out.println("method1메서드에서 예외가 처리되었습니다.");
            throw e;
        }
    }
}
```
### 실행 결과 (순서)
```declarative
method1메서드에서 예외가 처리되었습니다.
main메서드에서 예외가 처리되었습니다.
```

    반환 값이 있는 return문의 경우, catch블럭에도 return문이 있어야 한다
    예외가 발생했을 경우에도 값을 반환해야 하기 때문이다
    
    catch블럭에서 예외 되던지리를 해서 호출한 메서드로 예외를 전달하면
    > return문이 없어도 된다
    > 검증에서도 assert문 대신 AssertError를 생성해서 던진다

    finally블럭 내에도 return문을 사용할 수 있다
    try블럭이나 catch블럭의 return문 다음에 수행된다
    최종적으로 finally블랙 내의 return문의 값이 반환된다

---

### 연결된 예외 (chained exception)
##### 한 예외가 다른 예외를 발생시킬 수도 있다
예외 A가 에외 B를 발생시켰다면, A를 B의 ***원인 예외***라고 한다

```declarative
try {
    startInstall();
    copyFIles();
} catch (SpaceException e) {
    InstallException ie = new InstallException("설치중 예외발생");
    ie.iniCause(e);
    throw ie;
} catch (MemoryException me) {
    ...
}
```
1. InstallException을 생성
2. initCause()로 SpaceException을 InstallException의 원인 예외로 등록
3. `throw`로 예외를 던진다

###### 발생한 예외를 처리하지 않고 원인 예외로 등록해서 다시 예외를 발생시키는 이유
`여러가지 예외를 하나의 큰 분류의 예외로 묶어서 다루기 위해서`
+ InstallException을 SpaceException과 MemoryException의 조상으로 해서 catch블럭을 작성
+ 실제로 발생한 예외가 어떤 것인지 알수 없다
+ SpaceException과 MemoryException의 상속관계를 변경해야 한다
`그래서 예외가 원인 예외를 포함할 수 있게 한다`
`cheched 예외를 uncehcked 예외로 바꿀 수 있도록 하기 위함이다`
