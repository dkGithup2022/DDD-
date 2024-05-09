# 도메인 주도 개발 철저입문 요약 .


1. [도메인 주도 설계란?](#도메인-주도-설계란)
    - [도메인 모델링](#도메인-모델링)
        - [도메인 모델](#도메인-모델)
        - [도메인 객체](#도메인-객체)
    - [값 객체](#값-객체)
        - [값 객체의 예시와 장점](#값-객체의-예시와-장점)
        - [값의 성질과 구현](#값의-성질과-구현)
            - [값의 불변성](#값의-불변성)
            - [교환 가능하다](#교환-가능하다)
            - [등가성 비교하기](#등가성-비교하기)
            - [속성을 추가해도 수정이 필요없다](#속성을-추가해도-수정이-필요없다)
            - [행동이 정의된 값 객체](#행동이-정의된-값-객체)
2. [생애주기를 갖는 객체 - 엔티티](#생애주기를-갖는-객체---엔티티)
    - [엔티티란?](#엔티티란)
        - [가변이다](#가변이다)
        - [속성이 같아도 구분할 수 있다](#속성이-같아도-구분할-수-있다)
        - [엔티티의 판단 기준](#엔티티의-판단-기준)
3. [도메인 서비스](#도메인-서비스)
    - [도메인 서비스란?](#도메인-서비스란)
    - [도메인 서비스를 남용한 결과](#도메인-서비스를-남용한-결과)
4. [리포지토리 - 데이터관련 처리의 분리](#리포지토리---데이터관련-처리의-분리)
    - [레포지토리 책임 & 레포지토리 인터페이스 만들기](#레포지토리-책임--레포지토리-인터페이스-만들기)
    - [테스트](#테스트)
    - [Fake 레포 만들기](#fake-레포-만들기)
5. [어플리케이션 만들기](#어플리케이션-만들기)
    - [도메인 객체 개발하기](#도메인-객체-개발하기)
    - [서비스 개발하기](#서비스-개발하기)
        - [도메인 서비스 개발하기](#도메인-서비스-개발하기)
        - [어플리케이션 서비스](#어플리케이션-서비스)
6. [애그리거트](#애그리거트)
    - [애그리거트 경계 정하기](#애그리거트-경계-정하기)
    - [로직을 모은다](#로직을-모은다)
    - [내부 데이터를 숨긴다](#내부-데이터를-숨긴다)
    - [Notification 모델 정의 하기](#notification-모델-정의-하기)
    - [Notification 사용하기](#notification-사용하기)
7. [명세](#명세)


# 도메인 주도 설계란 ?

- "이용자 관점"의 유ㅇ한 지식을 코드에 녹여 넣어라
    - ex) 물류라면 , 트럭의 적제량 등 ... 운송 시간 등 ..

- 이런 이용자 관점의 지식을 코드에 넣고 관리할 수 있게 만들어 주는 방법론



## 도메인 모델링

### 도메인 모델
현실에서 일어나는 사건 & 개념을 추상화.

추상화에서 중요한 것은 , 유용한 정보만 정보화 시키는 것 (상품으로서의 차 와 배송에서의 차 는 표현 스펙이 다를 수밖에 없다)


### 도메인 객체
도메인 모델을 소프트웨어로 표현한 것


# 값 객체

### 값 객체의 예시와 장점

"값" 의  스펙을 정의한 클래스

```java

@Getter
@NoArgsConstructor
public class Name {

	private String firstName;
	private String middleName;
	private String lastName;

	public getFullName() {
			return firstName " " + middleName + " " + lastName
	}
	...
}


```


위의 예시는 "김 도현" , "dan kim" 으로 원시타입으로 저장한 것보다 아래의 점에서 강점을 가진다.

1. "성" 이 뭔지 더 확실한 구분, 원시타입에서는 두 이름의 "kim" 이라는 성을 분리해 낼 수 없다.
2. 로직의 관리 : 풀네임 출력, 이름의 validation 이 여러 곳에 있지 않고 한 곳에서 관리될 수 있다.
3. 의미단위가 명확해짐.  아래의 코드를 보자


```java

String dankim1 = "dan kim";
String Name dankim2 = someName;

```

dankim2 가 Name 이라는 의미가 더 명확하다. 파라미터로 던지거나 로직이 길어질 경우에도 어떤 목적의 변수인지 확신할 수 있다.


## 값의 성질과 구현

개발자가 말하는 "값"은 주로 아래의 성질을 가지고 있다.

1. 변하지 않는다.
2. 주고받을 수 있다.
3. 등가성을 비교할 수 있다.


### 값의 불변성

이것은 절대적인 강제이라기 보다는 무언의 약속에 가깝다.
만약 값이 수정된다면 값을 안심하고 사용할 수 없기 때문이다.

따라서 이 책의 저자는 아래같은 표현을 막으라고 추천한다.

```java

Name myName = new Name("dan","kim");

myName.changeFirstName("DDan") ; #-> 이런것을 하지 말라는 뜻. 

```


만약 값을 바꾸고 싶다면 새로운 객체를 생성하고 그 위치를 대신하게 하는 것을 추천한다.

사실 "꼭 이렇게 해야하나요?, 그냥 하면 더 편한데" 라던가 "필드하나만 바꾸면 되는데 10Kb 짜리 객체를 처음부터 만들라는 뜻이냐." 라던가 하는 의문이 생기는 부분일 수도 있지만 , 저자는 미래의 버그를 방지하기 위한 방어책으로 불변을 강조하고 있다.

만약 위의 의문처럼 꼭 가변 객체로 만들어야 하는 상황이 있다면 , 나중에 문제가 생겼을때 예외적으로 바꾸는 방책을 생각하는 것을 추천하고 있다.


### 교환 가능하다 .

불변이더라도 수정이 필요할 수는 있다. 아래처럼 새로운 객체를 생성해서 바꾸도록 하자 .

```java
Name myName = new Name("dan","kim");
myName = new Name("DDAN","kim");

```



### 등가성 비교하기 .


"값" 이 같아도 개발자의 개입이 없으면 서로 다른 "객체" 라고 나올 수 있습니다.

가령 ,

```java
Name myName = new Name("dan","kim");
Name yourName = new Name("dan","kim");

assert myName.equals(yourName);  : raise Exception 
```

위의 내용을 보면 이름은 같은데, 객체의 주소가 다르기 때문에 동등성 비교에 실패하는 케이스입니다.

등가성을 비교하기 위해선 equals & hascode 의 오버라이드가 필요해요.

이건 책의 내용이 아니라 effective 자바의 코드를 가져와봤습니다.

...


### 속성을 추가해도 수정이 필요없다.

만약 Name 이라는 값의 스펙에 "nickName" 이라는 속성이 추가된다고 가정해봅시다.
만약 값의 객체로 정의되어 있지 않다면 이름을 명시한 모든 부분에 캐어가 들어가야 하지만 값 클래스를 두는 경우엔 값객체만 수정하면된다.

그 외에도 가령 각 스펙의 validation 로직이 바뀐다던가.. .(null check 이 들어가거나 길이 제한이 생기는 등) 하는 경우에도 응집도 있게 관리되기 때문에 캐어의 범위가 줄어든다.


```java

@Getter
@NoArgsConstructor
public class Name {

	private String firstName;
	private String middleName;
	private String lastName;

	public getFullName() {
			return firstName " " + middleName + " " + lastName
	}
	...
}


-> 

@Getter
@NoArgsConstructor
public class Name {

	private String firstName;
	private String middleName;
	private String lastName;

	private String nickName; 

	public getFullName() {
			return firstName " " + middleName + " " + lastName
	}
	...
}



```


## 행동이 정의된 값 객체

값 객체에서 중요한 점 중 하나는 독자적인 행위를 정의할 수 있다는 점이다.

가령 아래의 돈 예제를 보자.


```java
class Money{

	private Long amount;
	private Currency currency;
	
	public Money ( Long amount, String currency ){
		if( amount < 0 )
			throw new IllegalArgumentException();

		this.amount  = amount; 
		thi.currency = currency;
	}

	public static Money add( Money foo, Money bar ) {
		if( foo.getCurrency() != bar.getCurrency())
			throw new IllegalArgumentException();

		return new Money (foo.amount() + bar.amount() , foo.currency);
	}
}

```


위의 예시는 두가지 행위를 강제하고 있다.


1. 생성 시, 음수 돈을 생성해선 안된다.
2. 덧셈 시, 단위가 맞아야 한다.


행동을 한곳에 모아둠으로써  값 객체를 통해서도 규칙을 강제하고 오류를 방지할 수 있다.


### 정의되지 않았기 때문에 알 수 있는 것

위 값 타입에서, 곱셈은 정의하지 않았다.
해당 값 객체의 사용자는 "곱셈은 허용되지 않았나? (캐어되지 않았나?)" 라고 유추할 수도 있다.


개발 시 타인이 작성한 라이브러리, 모듈위에서 개발하는 경우도 많기 때문에 이러한 힌트는 도움이 된다.



# 생애주기를 갖는 객체 -  엔티티

(도메인 주도 개발의 엔티티!,  jpa 엔티티가 아니다! )

잠시 Jpa 나 er-diagram 의 엔티티는 잊도록 하자 ㅎ
아래에 설명할 것은 ddd 의 엔티티이다.



## 엔티티란?

도메인 모델을 구현한 도메인 객체를 말한다.
값 객체도 도메인 객체이지만 차이점은 "동일성" 의 식별에 있다.

값 객체는 특정 필드(들)가 달라지면 의미상으로는 다른 값이다. 가령 "dankim" -> "dan lee" 가 되면 의미상 다른 값이지만
학생이라는 도메인 스펙 중 이름이 바뀌었다고 해서 다른 값이 되는 것은 아니다. 이 경우는 사용자 속성이 바뀐 것이지 사용자 자체가 수정된 것은 아니다.


엔티티는 아래와 같은 성질이 있다.

1. 가변이다
2. 속성이 같아도 구분이 가능하다
3. 동일성을 통해 구별된다 .



## 가변이다

```java

public class User{

	Name userName;

	public void chaneUserName(Name newName){
		thi.userName = newName;
	}
}
	...
```


### 가변 규칙

1. 속성의 변경에는 되도록 setter 대신 change~ , add ~, 등의 변경 전용 함수를 만들어라 .
    1. 변경에는 규칙이 있어야 한다. setter 같은 모호한 이름보다는 변경의 목적과 범위를 분명히 하라
2. 유효성 검증을 해라
    1. (1) 의 변경 함수에 유효성을 검사를 넣어서 유효한 경우만 수정한다.
    2. 예외가 발생하는 경우를 미연에 방지해라.


따라서 위의 코드는 아래처럼 변경이 가능하다.

```java

public class User{

	Name userName;

	public void chaneUserName(Name newName){
		is( !isGoodName(newName))
			throw new IllegalArgumentException();
			
		this.userName = newName;
	}

	private boolean isGoodName(Name name){
		... 
	}
}
	...
```


## 속성이 같아도 구분할 수  있다.

식별자를 만들어서 구분한다. 식별자는 unique 하게 관리 되어야 한다.

식별자를 초기에 생성할 때, 아래와 같은 방법을 동원할 수 있다.

1. RDMS 에서 제공되는 sequence ID
2. snowflake 를 통한 id generator
3. uuid
4. 그 외 유저의 로직에 의해 만들어진 id ( 락이 필요 할 수 도 있음 )

따라서 위의 유저는 아래와 같이 바뀔 수 있다.


```java 
public class User{

	UserId userId;
	Name userName;

	public void chaneUserName(Name newName){
		is( !isGoodName(newName))
			throw new IllegalArgumentException();
			
		this.userName = newName;
	}

	private boolean isGoodName(Name name){
		... 
	}
}
	...

public class UserId{

	private String userId;

	public UserId(String value){
		if( value == null )
			throw new IllegalArgumentException )()..
		this.userId = userID;
	}

}

```


## 엔티티의 판단 기준.


값 객체와 엔티티는 모두 도메인 개념을 나타내는 객체라는 공통점이 있다.
차이는, 식별자로 식별이 되어야 하는가 이다.

이 구분을 위해 "생애주기" 가 있는지 없는지에 대해서 생각을 해보면 구분에 도움이 될 수 있다.

가령, 사용자는 생성되면 식별자가 달려서 유지되고 몇일 ? 몇년 뒤 실제로 삭제가 된 다음에야 죽는다.
생애주기가 있고, 연속성을 갖는다라고 할 수 있다.  따라서 엔티티로 판단할 수 있다.

저자의 추천은 가변적인 객체는 프로그램을 복잡하고 에러를 만드는 주범임으로 가능한 많은 부분을 불변으로 간단하게 남기는 것을 추천하기도 한다.

많은 경우, 도메인 객체는 엔티티로 해도 되고 값으로 해도 된다.  이 경우는 여러 말이 있지만 요약하자면 개발자가 "잘 - 알아서" 하기를 바란다
팁은 환경에 맞게 설계하라는 것이다. 값 객체에서도 설명했다시피

상품으로서 "차" 와 배송에서의 "차" 는 다르다.
각자의 환경에 맞게 개발하면 된다.

## 도메인 객체를 정의할 때의 장점.

1. 자기서술적인 코드가 된다.
2. 도메인 변경사항이 있을 시, 코드에 반영하기 쉽다.


# 도메인 서비스

어플리케이션에서 서비스란 "클라이언트를 위해서 무언가를 해주는 것" 으로 굉장히 모호한게 맞다
도메인 주도 개발에서 말하는 서비스는
1. 도메인을 위한 서비스
2. 어플리케이션을 위한 서비스

이다.  "서비스" 라는 단어를 말할 때, 위의 두 가지를 구분해서 생각하면 분류가 더 명확해 질 수 있다.


## 도메인 서비스란 ?

값 객체나 엔티티 같은  도메인 객체에서만 처리하기 어려운 로직을 해결해주는 서비스 이다.

가령,
- 중복 방지
- 다른 도메인 객체의 정보가 필요한 경우.


이런 경우를 도메인 객체에 책임을 맞기면 어색하다. 이유는

1. 자기 자신이 중복이 되었는지 확인한다? 라는 것에 대한 의미적인 모호함 ( 객체를 생성후 ? ,)
2. rdms, nosql 등의 의존성이 객체 자체에 포함된다는 책임으로서의 모호함.


위와 같은 이유에서 부자연스러움을 해결해 줄 수 있는 서비스 객체를 만드는 것이 유용하다.
이 경우, "서비스" 라는 단어는 "어플리케이션의 서비스" 라기 보다는 "도메인객체의 서비스" 가 된다 .


```java

public class UserService {
	private final UserRepo userRepo;

	public boolean exist (UserId userId){
		return userRepo.exist(userId);
	}
}

```


## 도메인 서비스를 남용한 결과.

도메인 서비스에서 중요한 점은 "도메인 객체 자체에서 처리하기 부자연스러운 요청" 에 대해서만 한정해야 한다는 점이다.

만약에 너무 많은 책임을 도메인 서비스에 둔다면 아래처럼 될 수 있다 .

```java

# 유저 서비스에서 값의 변경까지 하는 경우. 

public class UserService{

	public void changeName(User user, Name name){
		if(user==null) throw new IllegalArgumentException ();
		if(name ==null || !name.isGoodName ) throw new IllegalArgumentException(); 
	}

	user.userName = name; 
}

...

# 위의 로직에 따른 유저 서비스의 모양 

@Getter
public class User{

	private Long userId;
	private Name name;
	
}
```


개터, 세터만 남는다면 유저 클래스는 자기 서술적인 객체가 되는데 실패한다.

저자가 추천하는 올바른 예시는 아래와 같다.



 ```java
@Getter
public class User{

	private Long userId;
	private Name name;

	public User(Long userId, Name name){
		if(userId == null)
			throw new IllegalArgumentException();
		if(name == null )
			throw new IllegalArgumentException();
		if(!isGoodName(name))
			throw new IllegalArgumentException();
		this.userId = userId;
		this.naem = name; 
	}
	

	public void changeUserName(Name name){
		if(name!= null && isGoodName(name))
			this.name =name;
	}
}
 
```


저자의 생각은 가능하면 도메인 서비스를 피하는 것이다.
도메인 엔티티 객체만 보고, 자기서술적인 코드를 만들 수 있어야 하는 것이 좋은 접근이라는 것이 저자님의 주장이다.

실제로 도메인 서비스를 남발하면 데이터와 행위의 연속성이 없어진다. 여기저기 로직이나 중요한 정보가 흩어진다는 뜻이다.
관리차원에서 비용을 줄이기 위해 이러한 행위는 지양해야한다는 것이다.


# 리포지토리 - 데이터관련 처리의 분리

엔티티는 생애주기를 같는 프로그램이기에  영속적인 정보의 처리를 관리하는 부분이 필요하다.
rdms, nosql 처럼 외부의 데이터 스토어에 대한 처리를 추상화하는 객체로 레포지토리에 대한 분리가 필요하다 .


레포지토리의 특징은 , 도메인 객체와는 연관성이 덜 하다. 테이블의 스펙과 도메인 객체는 차이가 있을 수 있기 때문인데,
오히려 이렇기 때문에 sql 관련한 부분을 모아 서비스 로직에 sql 관련 부분이 침식하는 것을 막는 역할도 할 수 있다.


레포지토리는 주제만 적을래요 ㅎ
### 레포지토리 책임 & 레포지토리 인터페이스 만들기

저장소에 대한 인터페이스, 도메인 객체를 저장, 조회하는 인터페이스
레포 인터페이스의 장점은 아래와 같다.


1. 레이어간 제공받는 기능의 스펙이 뚜렷해진다.
2. fake 레포를 만들 수 있다 ( 테스트 )



``` java

public interface IUserRepository{

	boolean exist(Long userId);
	void create(String userName);
	
}

```


여기서 중요한 것은 repository 의 책임은 퍼시스턴스와 관련한 요청까지라는 것이다.
따라서 checkJoinPossible() 이런 의미 단위기능을 레포지토리 스펙에 추가하지는 말자.
### 테스트

테스트 코드 짜세요.


### fake 레포 만들기

h2 를 쓰기 싫다면 ? 외부 의존으로 격리되고 싶다면 repository 를 fakerepo 로 변환할 수 있습니다 .


```java
public class InMemoryUserRepo implments IUserRepository{


	public HashMap<Long, User> db = new HashMap();

	public boolean exist(Long userId){...}
	public void create(String userName){...}
}



---

public class DoUserTest{

	private HashMap db = new InMemoryUserRepo();


	@Test
	void doSomethingWith_db(){
		db.exist(...)
	}

}
```


이런 테스트도 가능하다.

fake 레포의 장점은 아래와 같다.

1. 진짜 외부와 격리되기 때문에 빠르고 순수하게 테스트가 가능하다.
2. 한번 fake 클래스 만들어두면 재사용도 가능하다.
3. fake 에서 한번 로직을 바꿔 놓는게 mock 으로 각 부분을 일일이 바꾸는것보다 손이 덜 갈수도 있다.

### ORM 사용



# 어플리케이션 만들기 .


## 예시  포인트 .

### 설계 포인트
- 레이어링
    - application 서비스
    - 도메인 서비스
    - 레포지토리
    - 도메인
- 도메인 객체 분리
    - 불필요하게 도메인 객체를 노출하지 말자.


### 제공 기능

- 사용자 등록
- 사용자 조회
- 사용자 정보 수정
- 탈퇴.


## 도메인 객체 개발하기

자기 서술적인 도메인 객체를 작성하도록 한다.
조회 모델을 만들어서, api 의 응답을 통해 도메인의 함수를 호출 할 수 없게 만들자.


### 자기 서술적인 도메인 모델

각 스펙의 조건과 제공 함수(기능)을 도메인 객체에 되도록 포함 시킨다.

```java

public class User{

	private Long userId;
	private Name userName;

	public User (Long userId, Name userName){
		if(userId == null || !isGoodUserName(userName))
			throw new IllegalArgumentException();
		this.userId = userId;
		this.userName =userName;
	}

	public void changeUserName(Name userName){
		if(!isGoodUserName(userName))
			throw new IllegalArgumentException();
		this.userName = userName; 
	}

}

```

### 조회용 dto

만약 User 객체를 직접 api 의 응답으로 노출하게 되면 changeUserName 등의 함수를 호출받는 쪽에서 임의로 호출할 수도 있다.
이를 방지하기 위해 순순한 조회용 레코드를 만들자.

레코드 객체의 생성에 손이 가는 것을 방지하기 위해, 생성자,  static of 등의 함수는 미리 만들어두자.
```java

public record UserData(Long userId, Name userName){

	public UserData( User user){
		this.userId = user.getUserId();
		this.name = user.getUserName();
	}

}

```



## 서비스 개발하기


### 도메인 서비스 개발하기 .

```java

@Service
@RequiredArgsConstructor
public class UserService{

	private final IUserRepository userRepository ;

	public bool exist(Long userId){
		Optional foundOne = userRepository.findById(userId);
		return foundOne.exist();
	}

}

```



### 어플리케이션 서비스


```java

@Service
@RequiredArgsConstrictor
public class UserApplicationService{

	private final UserService userService;
	private final IUserRepository userRepository 

	public UserData getUser(Long userId){
		User user = userRepository.findById (userId)
			.orElseThrows(IllegalArgumentException::new);
			
		return new UserData(user);
	}

	public void update(UpdateCommand command){
		User user = userRepository.findById (command.userId)
			.orElseThrows(IllegalArgumentException::new);

		if(!command.userName().isEmpty)
			user.changeUserName(command.userName);

		if(!command.userEmail().isEmpty)
			user.changeUserEmail(command.userEmail);

		userRepository.save(user);
	}
}

```


### 만약에 등록을 별도 클래스로 둔다면 ...

응집도를 높이거나 , 서비스 클래스가  지나치게 커지는 것을 막기위해 기능별로 혹은 비슷한 기능별로 별도의 클래스를 둘 수 있다.
아래의 예시는 등록만 분리한 것이다 .


```java

@Service 
@RequiredArgsConstructor
public class UserRegisterService 
{

	private final UserService userService ;
	private final IUserRepository userRepository;


	public void handle(UserRegisterCommand command){
	
		if( userService.exsits(userId) )
			throw new IllegalArgumentException();
		var user = new User(cmd.userName());
		userRepository.save(user);
	}
}
```


# 애그리거트

객체지향에서는 여러 객체가 모여 하나의 진짜 의미를 갖는 객체가 된다.
이런 관계에서 각 객체는 각자의 불변조건을 항상 유지해야 한다.

이 불변조건은 언제나 유지돼야 한다. 그리고 변화하는 환경에서 이를 달성하기 위해선 규칙이 필요하다.

애그리거트는 불변 조건을 유지하는 단위이고, 이를 기점으로 객체 조작의 질서를 유지한다.

애그리거트는 경계와 루트를 같는다.
- 경계: 대상의 범위
- 루트: 외부에서 호출하는 대상 ( 되도록 루트만 호출하게 강제)

### 애그리거트 기본 구조

외부에서는 애그리거트 내부의 객체를 직접 조작하게 허용해서는 안된다.
애그리거트 루트의 인터페이스를 통해서 조작하는 것이 권장된다.


```java

Group group = getGroup();

group.userAdd(user); // ok 


group.getUsers().add(user) // NG 
```

위의 예시처럼 직접 필드에 접근해서 뭔가를 하려는 행위는 막아야한다.

### 로직을 모은다.

왜냐하면 불변성을 지킬 수 없고, 로직이 산발되버리기 때문이다.
Group 은 유저의 숫자에 제약이 있다고 가정해보자 .


```java

public class Group {

	private List<User> users;


	public void addUser(User user ){
		if(users.size()>10)
			throw new IllegalArgumentException();
		users.add(user);
	}

}

```

가령 위처럼, users 에 대한 validation 이 들어가는 경우입니다. 직접 users 를 가지고 조작한다면 이러한 로직을 호출하는 위치에 따로 가지게 됩니다.


데메테르의 법칙이라고 하는 것이 있다.
어떤 컨텍스트에서 메소드를 호출할 때, 다음 객체의 메소드만 호출하게 한다.

1. 객체 자신
2. 인자로 받은 객체
3. 인스턴스 변수
4. 해당컨텍스트에서 생성한 객체


### 내부 데이터를 숨긴다.


" 필요한 정도만 오픈한다 " 라는 것은 당연하지만 사실 이루기는 어려운 문제다.
가령 , User 객체의 필드 중 조회되어야 하는 부분만 Getter 를 제작한 경우 User 를 save 는 어떻게 할 것인가?
등의 문제가 있다 .

개발자들의 규칙으로 우리 getter 는 오픈하되, retreieve 함수가 명시된 것만 서비스로직에서 쓰죠 ? 할 수도 있지만 강제성이 없다.

다른 방법은 노티피케이션 객체를 쓰는 것이다.


1. 노티피케이션  인터페이스를 정의한다.
2. 노티피케이션 데이터 클래스를 생성한다.
3. User 에서 notification 클래스를 초기화 하는 함수를 제공한다.

즉, 내부 객체의 로직이나 다른 군더더기 없이 순수하게 정보성객체와 primitive 한 값만 프로젝션된 객체를 생성한다.

손이 좀 간다.


### notification 모델 정의 하기

```java
public interface UserNotification {
	void id(Long id);
	void userName(Name name);
}

public class UserDataModelBuilder implements UserNotification {

	private Long userid;
	private Name userName;

	void id(Long id) {this.userId =id}
	void userName(Name name) {this.userName =name};
	public UserDataModel build(){
		return new UserData(userId,userName);
	}

}

public class User{

....

	public void notify(UserNotification noti){
		noti.id(id);
		noti.name(name);
	}

}
```


### notification 사용하기 .

```java
public class UserRepository implements IUserRepository {

	UserJpaRepository userjpaRepository;

	public void save(User user){
		var modelBuilder = new UserDataModelBuilder();
		user.notify(modelBuilder);
		var model = modelBuilder.build();
		userJpaRepository.save(model);
	}
}

```


## 애그리거트 경계 정하기

- 변경의 단위 .
- 주관적이고 정해진 것은 없다.

- 애그리거트 바깥의 내용을 가질땐, 식별자로만 소유하게 해서 느슨하게 만드는 것이 좋다.
- 잘못된 싸인
    - 애그리거트 범위를 넘나드는 요청이 잦다.
    - 퍼시스턴스 래밸을 억지로 맞워야 한다.
    - 지나치게 애그리거트가 커져서 락의 범위도 커지는 경우



# 명세

- 특정 조건, 상태를 나타내는 로직이 길어진다면 별도 클래스로 가질 수 있다.


가령 Group 의 추가 여부는 아래와 같다고 하자.

- 기본적으로 30명까지
- Premium user 가 10 명 이상이라면 50 명까지.
- 만든지 1주일 미만이라면 10명까지


```java

@Service
public class GroupFullSpecification{

	private final IUserRepository userRepository;

	public bool isFull(Group group){
		var users = userRepository.findAllByUserId(group.getUserIds());
		var premiumUsers = users.stream.filter(e->e.status == UserStatus.PREMIUM).collec(toList());
		var beginDate = group.getCreatedAt();

		if(premiumUsers.size()>=10)
			return group.size() <50;
		if(beginDate.addDays(7).isBefore(LocaDateTime.now()))
			return group.size() < 10;

		return group.size() < 30;
	}
}

```


일급 컬렉션을 사용하여 추상화의 래밸을 올릴 수도 있다. 