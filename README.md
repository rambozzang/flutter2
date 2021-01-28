# flutter Data class 변환 싸이트
1. https://javiercbk.github.io/json_to_dart/
2. https://app.quicktype.io/



# Getx 함수 비교 
Obx vs. GetBuilder vs. GetX

```dart
class Log2Page extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    Controller c = Get.put(Controller());

    return Scaffold(
      body: SafeArea(
        child: Center(
          child: Column(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: [
              Obx(
                      () => Text('Obx: ${c.log2.value}')
              ),
              // ↓ requires manual controller.update() call
              GetBuilder<Controller>(
                builder: (_c) => Text('GetBuilder: ${_c.log2.value}'),
              ),
              // ↓ controller instantiated by Get widget
              GetX<Controller>(
                init: Controller(),
                builder: (_c) => Text('GetX: ${_c.log2.value}'),
              ),
              RaisedButton(
                child: Text('Add +1'),
                onPressed: c.change,
              ),
              RaisedButton(
                child: Text('Update GetBuilder'),
                onPressed: c.update, // rebuild GetBuilder widget
              ),
            ],
          ),
        ),
      ),
    );
  }
}

class Controller extends GetxController {
  RxInt log2 = 0.obs;

  void change() => log2.value++;
}
```

## Obx

Listens to observable (obs) changes. Controller needs to already be declared/initialized elsewhere to use.

관찰 가능한 (obs) 변화를 수신합니다. 컨트롤러를 사용하려면 다른 곳에서 이미 선언 / 초기화해야합니다.



## GetX

Listens to observable (obs) changes. Can initialize controller itself using init: constructor argument, if not done elsewhere. Optional argument. Safe to use init: if Controller already instantiated. Will connect to existing instance.

관찰 가능한 (obs) 변화를 수신합니다. init : constructor 인수를 사용하여 컨트롤러 자체를 초기화 할 수 있습니다.
선택적 인수. init 사용이 안전합니다 : Controller가 이미 인스턴스화 된 경우. 기존 인스턴스에 연결됩니다.


## GetBuilder

Does not listen to obs changes. Must be rebuilt manually by you, calling controller.update(). Similar to a setState() call. Can initialize controller itself using init: argument, if not done elsewhere. Optional.

obs 변화를 듣지 않습니다. controller.update ()를 호출하여 수동으로 다시 빌드해야합니다. setState () 호출과 유사합니다.
다른 곳에서 수행하지 않으면 init : 인수를 사용하여 컨트롤러 자체를 초기화 할 수 있습니다. 선택 과목.




