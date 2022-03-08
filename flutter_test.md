![alt text](https://miro.medium.com/max/698/1*fbGTFlIrgfwmNVzHI-Wwbw.png)

## **Flutter testing**

**Best Practices for widget testing**
- Test for what the users see, not implementation details. So, generally, aim to find widgets by a string or icon, not a key or a widget type. There are exceptions, but most of your tests should test for strings and icons.
  ```
  //Example =>
  void main() {
  testWidgets('MyWidget has a title and message', (WidgetTester tester) async {
    await tester.pumpWidget(const MyWidget(title: 'T', message: 'M'));
    final titleFinder = find.text('T');
    final messageFinder = find.text('M');

    // Use the `findsOneWidget` matcher provided by flutter_test to verify
    // that the Text widgets appear exactly once in the widget tree.
    expect(titleFinder, findsOneWidget);
    expect(messageFinder, findsOneWidget);
     });
    }
   ```
- Set up a helper methods for writing widget tests. Eg no one wants to tap then pump a widget, it’s not concise, create a helper method for that.
- Set up a way to easily mock a specific API call. 
  ```
  Use mockito package to mock API call(asynchronous calls)
  Example =>
    class Cat {
  String sound() => "Meow";
  bool eatFood(String food, {bool? hungry}) => true;
  Future<void> chew() async => print("Chewing...");
  int walk(List<String> places) => 7;
  void sleep() {}
  void hunt(String place, String prey) {}
  int lives = 9;
    }
  ```
  ```
    //cat_test.dart
    import 'package:mockito/annotations.dart';
    import 'cat.dart';

    @GenerateMocks([Cat]) //Mocking Cat class
    void main() {

    }
  ```
  ```
  //Run dart command 
  dart run build_runner build
  ```
  ```
  //Initialise an object of MockCat in main method of cat_test.dart
  var cat = MockCat();
  ```
  ```
  //Verify the sound method 

    test('verify sound',() {
    when(cat.sound()).thenReturn('meow');

    cat.sound();

    verify(cat.sound());
    });
  ```
  >Note: Always re-generate mock class after altering the class that you are mocking.

  >Always Stub the methods before verifying it in a test block. Otherwise, it will return an error like the following.
  ![Alt text](images/stubbing_error.png)
- Set up a helper methods for common initial scenarios that rely on some data saved on device, such as a logged in user.

**Grouping test methods**

>Programmers should group test methods that are related to one another.

```
// Example =>
import 'package:test/test.dart';
import 'package:counter_app/counter.dart';

void main() {
  group('Counter', () {
    test('value should start at 0', () {
      expect(Counter().value, 0);
    });

    test('value should be incremented', () {
      final counter = Counter();

      counter.increment();

      expect(counter.value, 1);
    });

    test('value should be decremented', () {
      final counter = Counter();

      counter.decrement();

      expect(counter.value, -1);
    });
  });
}
```
**Use of setUp() and tearDown() method**
> Programmers can use the setUp() and tearDown() functions to share code between tests. The setUp() callback will run before every test in a group or test suite, and tearDown() will run after. tearDown() will run even if a test fails, to ensure that it has a chance to clean up after itself
```
// Example =>
import 'package:test/test.dart';

void main() {
  late HttpServer server;
  late Uri url;
  setUp(() async {
    server = await HttpServer.bind('localhost', 0);
    url = Uri.parse('http://${server.address.host}:${server.port}');
  });

  tearDown(() async {
    await server.close(force: true);
    server = null;
    url = null;
  });

  // ...
}
```