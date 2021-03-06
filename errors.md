![alt text](https://storage.googleapis.com/cms-storage-bucket/70760bf1e88b184bb1bc.png)

**This documnet covers the following errors**
- [**Problems with type check**](#problems-with-type-check)
- [**No Element found error**](#no-element-found-error)
- [**Type parsing**](#type-parsing)
- [**Future methods Error Handling**](#future-methods-error-handling)
- [**Try-Catch**](#try-catch)
- [**Common Flutter Errors**](#common-flutter-errors)
## **Problems with type check**

- ***A variable is statically known to be some supertype, but the code assumes a subtype***
  
Example => 
```
void main() {
  var _person1=getPerson();
  var _person=_person1;

  print(_person.toName);
}

class Person{
  final int id;
  final String name;

  Person(this.id, this.name);
}

extension Name on Person{

  String get toName{
    return this.name;
  }
}
getPerson(){
  return Person(1,"Rahul");
}
```

>On its execution, it will throw a runtime exception

`Uncaught TypeError: (intermediate value).get$toName is not a functionError: TypeError: (intermediate value).get$toName is not a function`

>To fix this error, a programmer need to replace the definition of the member with an explicit type declaration

Example => 
```
void main() {

  var _person1=getPerson();
  var _person=_person1 as Person;

  print(_person.toName);
}
```

- ***Missing type arguments***

```
class Superclass<T> {

  void method(T param) { ... }
}

class Subclass extends Superclass {

  @override
  void method(int param) { ... }
}
```
>In the above example, Subclass extends Superclass<T> but doesn’t specify a type argument. The analyzer infers Subclass<dynamic>, which results in an invalid override error on method(int).

>To fix this error, programmers need to provide the type
 Example => 

 ```
 class Subclass extends Superclass<int>{

  @override
  void method(int param) { ... }
}
 ```

- ***Unexpected collection element type***

Example =>
 ```
 void main(){

  var map ={"a":1, "b":2,"c":1};
  map['c']=4.4;

  print(map['c']);
}
 ```
 >In the above example the analyser infers the map as a Map<String, int> but the code assumes it as Map<String, dynamic> or Map<String, num> due to which the compiler throws an error as the compiler was expecting Map<String, int> but the programmer provides Map<String, dynamic> or Map<String, num>

 >To fix this issue, the programmer needs to explicitly specify the type of the map

 Example =>

 ```
 var map =<String, dynamic>{"a":1, "b":2,"c":1};

 // or

 var map =<String, num>{"a":1, "b":2,"c":1};
 ```

 ## **No Element found error**

 Expample =>

 ```
 //finding an element that doesn't exist
 void main() {
      List<int> numbers = [1, 2, 3];
      int result = numbers.firstWhere(
        (val) {
          if (val == 23) {
            return true;
          } else {
            return false;
          }
        },
      );
      print(result);
    }
 ```

 ```
  //finding first element from an empty list
  void main(){
    List<int> numbers = [];
    var result=numbers.first;
    print(result);
  }
 ```

 >The above examples will throw an Exception `Bad state: No element` as the analyser tries to get the data but it could not find it.

 To solve this issue, programmers must pass optional argument orElse of the ***`firstWhere()`*** and in case of an empty list they need to use `firstOrNull` extension method of `collection` package

 Example => 

 ```
      List<int> numbers = [1, 2, 3];
      int result = numbers.firstWhere(
        (val) {
          if (val == 23) {
            return true;
          } else {
            return false;
          }
        },
        orElse: (){
          return -1;
        }
      );
      print(result);
 ```

 ```
  import 'package:collection/collection.dart';

      List<int> numbers = [];
      int result = numbers.firstOrNull();
      print(result);
 ```

 ## **Type parsing**
  
Example =>
```
int a=int.parse("43s");
```
The above example will throw `FormatException`

>To fix this programmers should use ***`tryParse()`*** which will return null in the case where the given string cannot be parsed.

Example =>
```
int? a=int.tryParse("43s")
```
here the value of a will be null

## **Future methods Error Handling**

``` 
Future<int> myFunc(){
    ....
    ....
}
```

Future functions can come with value, empty or error.

```
myFunc().then((val){

    .... //do something here
    ....

}).catchError(errorHandler);

```

>Here ***`catchError()`*** callback gets fired if myFunc() comes with an error

>if within the ***`then()`***  method, programmer is performing any operation that can throw an error, then that error can also be handled at ***`catchError()`*** callback

```
myFunc().then(successCallback, onError: (e) {

  handleError(e); // Original error.

  anotherFuncThatThrows(); // Oops, new error.

}).catchError(handleError);
```

>onError callback within ***`then()`*** will handle the error that comes from ***myFunc()***

## **Try-Catch**

>To handle the runtime exception and to ensure that the program does not crash, try-catch block is used.

Example => 

```
foo(List<int> arr){
    return arr[10];
}
```

>Here in the above code, the function ***foo()*** can throw `Index out of range` exception. Now to prevent unwanted exception and to make smooth execution of the program, programmers need to handle the exception. To handle the exception, the statement(s) that can throw an exception(s) should be wrapped inside `try-catch` block

Example =>

```
foo(List<int> arr){
    try{

        return arr[10];

    }catch(e){

        //handle the exception
        ......
        ......

    }
}
```

## **Common Flutter Errors**
- ***A RenderFlex overflowed… Error***
  
  >When this error can be observed? 

  >The error often occurs when a Column or Row has a child widget that is not constrained in its size.

  Example =>

  ```
  Widget build(BuildContext context) {
  return SizedBox(
    child: Row(
      children: [
        Icon(Icons.message),
        Column(
          mainAxisSize: MainAxisSize.min,
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text("Title", style: Theme.of(context).textTheme.headline4),
            Text(
                "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed"
                " do eiusmod tempor incididunt ut labore et dolore magna "
                "aliqua. Ut enim ad minim veniam, quis nostrud "
                "exercitation ullamco laboris nisi ut aliquip ex ea "
                "commodo consequat."),
          ],
        ),
      ],
    ),
  );
  ```
>In the above example, since the `Row` widget doesn't have any size constraint the `Column` widget inside the Row will try to expand as much as possible, causing it to throw an error.

>To fix this issue, programmers need to give size constraints. To do this, the programmers can give fixed height to the `SizedBox` widget or they can wrap the Column widget inside an `Expanded` widget or Column widget can be Wrapped inside a `Flexible` widget

- **RenderBox was not laid out Error**
 
   The RenderBox was not laid out error is often caused by one of two other errors:

    1. ‘Vertical viewport was given unbounded height’
    2. ‘An InputDecorator…cannot have an unbounded width’
 1. ‘Vertical viewport was given unbounded height’ =>

    >The error is often caused when a ListView (or other kinds of scrollable widgets such as GridView) is placed inside a Column. A ListView takes all the vertical space available to it, unless it’s constrained by its parent widget. However, a Column doesn’t impose any constraint on its children’s height by default. The combination of the two behaviors leads to the failure of determining the size of the ListView.
    
    ```
    Widget build(BuildContext context) {
     return Center(
        child: Column(
          children: <Widget>[
            Text('Header'),
            ListView(
              children: <Widget>[
                ListTile(
                  leading: Icon(Icons.map),
                  title: Text('Map'),
                ),
                ListTile(
                  leading: Icon(Icons.subway),
                  title: Text('Subway'),
                ),
              ],
            ),
          ],
        ),
     );
    }
    ```

    >To fix this error, specify how tall the ListView should be. To make it as tall as the remaining space in the Column, wrap it using an Expanded widget (see the example below). Otherwise, specify an absolute height using a SizedBox widget or a relative height using a Flexible widget.

    ```
    Widget build(BuildContext context) {
      return Center(
        child: Column(
          children: <Widget>[
            Text('Header'),
            Expanded(
              child: ListView(
                children: <Widget>[
                  ListTile(
                    leading: Icon(Icons.map),
                    title: Text('Map'),
                  ),
                  ListTile(
                    leading: Icon(Icons.subway),
                    title: Text('Subway'),
                  ),
                ],
              ),
            ),
          ],
        ),
      );
    }
    ```
 2. ‘An InputDecorator…cannot have an unbounded width’ =>

    >When this error can be observed? 

    >This error occurs, for example, when a Row contains a TextFormField or a TextField but the latter has no width constraint.

    Example =>

    ```
    Widget build(BuildContext context) {
      return MaterialApp(
        home: Scaffold(
          appBar: AppBar(
            title:
                Text('Unbounded Width of the TextField'),
          ),
          body: Row(
            children: [
              TextField(),
            ],
          ),
        ),
      );
    }
    ```
    >This error can be fixed by constraining the text field using either an Expanded or SizedBox widget. The following example demonstrates using an Expanded widget.

- **Incorrect use of ParentData widget Error**

    >When this error can be observed? 

    >While Flutter’s widgets are generally flexible in how they can be composed together in a UI, a small subset of those widgets expect specific parent widgets. When this expectation can’t be satisfied in your widget tree, you’re likely to see this error.

    List of widgets that expects a specific parent widget

    | Widget | Expected Parent widget |
    | --- | ----------- |
    | Flexible | Row, Column, or Flex |
    | Expanded | Row, Column, or Flex |
    | Positioned | Stack |
    | TableCell | Table |
    
- **setState called during build Error**

    >The build method in your Flutter code is not a good place to call setState either directly or indirectly.

    Example =>
    ```
       Widget build(BuildContext context) {

     // Don't do this. showDialog implicitly calls setState()
     showDialog(
         context: context,
         builder: (BuildContext context) {
           return AlertDialog(
             title: Text("Alert Dialog"),
           );
         });

     return Center(
       child: Column(
         children: <Widget>[
           Text('Show Material Dialog'),
         ],
       ),
     );
    }
    ```    
    >The build method is not the right place to call showDialog because build can be called by the framework for every frame, for example, during an animation

    ```
        class FirstScreen extends StatelessWidget {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          appBar: AppBar(
            title: Text('First Screen'),
          ),
          body: Center(
            child: RaisedButton(
              child: Text('Launch screen'),
              onPressed: () {
                // Navigate to the second screen using a named route.
                Navigator.pushNamed(context, '/second');
                // Immediately show a dialog upon loading the second screen.
                Navigator.push(
                  context,
                  PageRouteBuilder(
                    barrierDismissible: true,
                    opaque: false,
                    pageBuilder: (_, anim1, anim2) => MyDialog(),
                  ),
                );
              },
            ),
          ),
        );
      }
     }
    ```

    >One way to avoid this error is to use the Navigator API to trigger the dialog as a route.

    Example =>
    ```
        class FirstScreen extends StatelessWidget {
      @override
      Widget build(BuildContext context) {
        return Scaffold(
          appBar: AppBar(
            title: Text('First Screen'),
          ),
          body: Center(
            child: RaisedButton(
              child: Text('Launch screen'),
              onPressed: () {
                // Navigate to the second screen using a named route.
                Navigator.pushNamed(context, '/second');
                // Immediately show a dialog upon loading the second screen.
                Navigator.push(
                  context,
                  PageRouteBuilder(
                    barrierDismissible: true,
                    opaque: false,
                    pageBuilder: (_, anim1, anim2) => MyDialog(),
                  ),
                );
              },
            ),
          ),
        );
      }
    }
    ```
