Angel framework 

Requests & Responses

Angel은 Express에서 영감을 얻었으며, Req 핸들러는 일반적으로 Express와 유사합니다.
Req핸들러는 모든 Dart 객체를 반활 할 수 있습니다(처리방법참조) 기본 요청 핸들러는 2가지 변수를  받습니다.

* @ RequestContext - Req 방법, Req 본문, IP 주소 등과 같은 리소스를 요청하는 client에 대한 중요한 정보를 포함.    Req Object 는 또한 한 핸들러에서 다음 핸들러로 정보를 전달하는데 사용 할 수 있습니다.

*    ResponseContext - Header 전송, 데이티 쓰기 등을 client로 전송 할수 있습니다. Response 이 핸들러에 의해 수정되는 것을 방지 하려면  Res.end() 를 호출 하여 추가 쓰기를 방지하세요.

Return Valuse

Req 핸들러는 모든 Dart 값을 반환 할 수 있습니다. 반환 값은 다음과 같이 처리됩니다.

*  bool을 반환하는 경우 : false를 반환하면 요청 처리가 조기에 종료되지만 true를 반환하면 계속됩니다.
*  null을 반환하는 경우 : res.close ()를 호출하여 응답 객체를 닫지 않는 한 요청 처리가 계속됩니다. res.redirect () 또는 res.serialize ()와 같은 일부 응답 메서드는 응답을 자동으로 닫습니다.
*  RequestHandler : 반환 된 핸들러가 실행됩니다.
*  Stream : toList가 호출 된 다음 반환됩니다.
*  Future : 기다렸다가 반환됩니다.
*  기타 : 반환하는 다른 Dart 값은 응답으로  serialized 됩니다. 기본 방법은 json.encode를 사용하여 응답을 JSON으로 인코딩하는 것입니다. 그러나 res.serializer = foo;를 설정하여 응답의 serialized 방법을 변경할 수 있습니다. 모든 응답에 동일한 직렬 변환기를 할당하려면 Angel 인스턴스에서 serializer 변환기를 전역적으로 설정하십시오.  Maps 또는 Lists와 같은 JSON 호환 Dart 개체 만 반환하는 경우 런타임 성능을 향상시키기 위해 JSON.encode를 serializer 로 삽입하는 것을 고려할 수 있습니다 (2.0의 기본값).


Other Parameters

Req 핸들러는 RequestContext 및 ResponseContext 대신 다른 매개 변수를 사용할 수 있습니다. 종속성 주입 문서를 참조하십시오.

Queries, Files and Bodies

RequestContext.queryParameters를 호출하여 URI 쿼리 매개 변수를 기반으로 변경 가능한 맵에 액세스 할 수 있습니다.
사용자 입력을 처리하는 방법을 이해하려면 본문 구문 분석 문서를 참조하십시오.


Dependency Injection

Angel은 DI에 컨테이너 계층 구조를 사용합니다. 종속성 주입을 사용하면 논리를 한 위치에 포함하고 응용 프로그램의 다른 위치에서 재사용 할 수 있으므로 여러 움직이는 부분이있는 응용 프로그램을 쉽게 빌드 할 수 있습니다.

Adding a Singleton

Future<void> myPlugin(Angel app) async {
app.container.registerSingleton(SomeClass("foo"));
app.container.registerSingleton<SomeAbstractClass>(MyImplClass());
app.container.registerFactory((_) => SomeClass("foo"));
app.container.registerLazySingleton((_) => SomeOtherClass());
app.container.registerNamedSingleton('yes', Yes());
}

또한 각각 앱의 전역 컨테이너에서 확장되는 컨트롤러 속성이 있으므로 RequestContext 내에 삽입 할 수도 있습니다.
이러한 삽입 된 속성에 액세스하는 것은 쉽고 강력하게 입력됩니다.

// Inject types.
var todo = req.container.make<Todo>();
print(todo.isComplete);

// Or by name
var db = await req.container.findByName<Db>('database');
var collection = db.collection('pets');

In Routes and Controllers

Angel 2.0에서는 ioc 호출에 함수를 래핑하여 모든  route handler 의 종속성을 자동으로 삽입 할 수 있습니다.
```dart
app.get("/some/class/text", ioc((SomeClass singleton) => singleton.text)); // Always "foo"

app.post("/foo", ioc((SomeClass singleton, {Foo optionalInjection}));

@Expose("/my/controller")
class MyController extends Controller {

@Expose("/bar")
// Inject classes from container, request parameters or the request/response context :)
bar(SomeClass singleton, RequestContext req) => "${singleton.text} bar"; // Always "foo bar"

@Expose("/baz")
baz({Foo optionalInjection});
}
```
