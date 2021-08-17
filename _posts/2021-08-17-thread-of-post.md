웹 어플리케이션 런타임 시 클래스가 제대로 로드되었는지 확인해보는 방법 (더 좋은 방법이 있을 수 있습니다.)

현재 스레드 가져오기
```
Thread currentT = Thread.currentThread()
```
 
Thread의 클래스로더 가져오기
```
ClassLoader loader = currnetT.getContextClassLoader()
```
 

클래스로더에 로드 되었는지 확인하기
```
loader.getResource(확인 원하는 클래스)
```
 

원하는 클래스 입력 시 패키지(경로), .class까지 다 넣어줘야한다.
예를 들면 text.class 확인하고싶은데 text.class는 com.my 패키지에 있다.


그럴 경우 loader.getResource("com/my/text.class") << 이렇게 입력해줘야 한다.

```
Thread currentT = Thread.currentThread();

ClassLoader loader = currentT.getContextClassLoader();

if(loader.getResource("com/my/text.class") != null){
	System.out.println("클래스 로드 되어있음.");
}else{
	System.out.println("클래스 로드 되어있지 않음.");
}
```
