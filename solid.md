## **SOLID Principle**
- [**S - Single Responsibility Principle**](#s---single-responsibility-principle)

- [**O - Open-Closed Principle**](#o---open-closed-principle)

- [**L -  Liskov Substitution Principle**](#l---liskov-substitution-principle)

- [**I -   Interface Seggaration Principle**](#i---interface-segregation-principle)

- [**D - Dependency Inversion Principle**](#d---dependency-inversion-principle)

 
## **S - Single Responsibility Principle**
**S - Single Responsibility Principle** states that a class should have only one job meaning, it should have only one reason to change

- For example, consider an application that takes a collection of shapes—circles, and squares—and calculates the sum of the area of all the shapes in the collection.

- First, create the shape classes and have the constructors set up the required parameters.

- For squares, you will need to know the length of a side:

```
class Square {
  late int sideLength;

  Square(int sideLength) {
    this.sideLength = sideLength;
  }
}
```

For circles, you will need to know the radius:

```
class Circle {
  late int radius;

  Circle(int radius) {
    this.radius = radius;
  }
}
```

- Next, create the AreaCalculator class and write up the logic, to sum up the areas of all provided shapes. The area of a square is calculated by length squared. The area of a circle is calculated by pi times radius squared.

```
class AreaCalculator {
  late List<dynamic> shapes;

  AreaCalculator(List<dynamic> shapes) {
    this.shapes = shapes;
  }

  double sum() {
    double area = 0.0;

    shapes.forEach((item) {
      if (item.runtimeType.toString() == 'Square') {
        area += (pow(item.sideLength, 2).toDouble());
      } else if (item.runtimeType.toString() == 'Circle') {
        area += (pi / 12.0 * pow(item.radius, 2).toDouble());
      }
    });

    return area;
  }

  String output() {
    return 'Sum of the areas of provided shapes:' + sum().toString();
  }
}
```

- To use the AreaCalculator class, you will need to instantiate the class and pass in an array of shapes and display the output at the bottom of the page.

Here is an example with a collection of three shapes:


a circle with a radius of 2
a square with a length of 5
a second square with a length of 6

```
List<dynamic> shapes = [
    Circle(2),
    Square(5),
    Square(6),
  ];

  var areas = AreaCalculator(shapes);

  print(areas.output());
```
- The problem with the output method is that the AreaCalculator handles the logic to output the data.

- Consider a scenario where the output should be converted to another format like JSON.

- All of the logic would be handled by the AreaCalculator class. This would violate the single-responsibility principle. The AreaCalculator class should only be concerned with the sum of the areas of provided shapes. It should not care whether the user wants JSON or HTML.

- To address this, you can create a separate SumCalculatorOutputter class and use that new class to handle the logic you need to output the data to the user:

```
class SumCalculatorOutputter {
  late AreaCalculator calculator;

  SumCalculatorOutputter(AreaCalculator calculator) {
    this.calculator = calculator;
  }

  String JSON() {
    var data = [
      {
        'sum': this.calculator.sum(),
      }
    ];

    return (data.toString() + "covert to JSON");
  }

  String HTML() {
    return this.calculator.sum().toString() + 'convert to HTML';
  }
}
```

- The SumCalculatorOutputter class would work like this:

```
List<dynamic> shapes = [
    Circle(2),
    Square(5),
    Square(6),
  ];

  var areas = AreaCalculator(shapes);
  var output = SumCalculatorOutputter(areas);

  print(output.JSON());
  print(output.HTML());
```

- Now, the logic you need to output the data to the user is handled by the SumCalculatorOutputter class.

- That satisfies the single-responsibility principle.

 
## **O - Open-closed Principle**
**O - Open-closed Principle (OCP)** states that objects or entities should be open for extension but closed for modification. This means that a class should be extendable without modifying the class itself.

- Let’s revisit the AreaCalculator class and focus on the sum method:

```
class AreaCalculator {
  late List<dynamic> shapes;

  AreaCalculator(List<dynamic> shapes) {
    this.shapes = shapes;
  }

  double sum() {
    double area = 0.0;

    shapes.forEach((item) {
      if (item.runtimeType.toString() == 'Square') {
        area += (pow(item.sideLength, 2).toDouble());
      } else if (item.runtimeType.toString() == 'Circle') {
        area += (pi / 12.0 * pow(item.radius, 2).toDouble());
      }
    });

    return area;
  }
}
```

- Consider a scenario where the user would like the sum of additional shapes like triangles, pentagons, hexagons, etc. You would have to constantly edit this file and add more if/else blocks. That would violate the open-closed principle.

- A way you can make this sum method better is to remove the logic to calculate the area of each shape out of the AreaCalculator class method and attach it to each shape’s class.

- Here is the area method defined in Square:

```
class Square {
  late int sideLength;

  Square(int sideLength) {
    this.sideLength = sideLength;
  }
  double area() {
    return pow(this.sideLength, 2).toDouble();
  }
}
```

- And here is the area method defined in Circle:

```
class Circle {
  late int radius;

  Circle(int radius) {
    this.radius = radius;
  }
  double area() {
    return pi / 12.0 * pow(this.radius, 2);
  }
}
```

- The sum method for AreaCalculator can then be rewritten as:

```
class AreaCalculator {
// ...
  double sum() {
    double area = 0.0;

    shapes.forEach((item) {
      area += item.area();
    });

    return area;
  }

 //...
}
```

- Now, you can create another shape class and pass it in when calculating the sum without breaking the code.

- However, another problem arises. How do you know that the object passed into the AreaCalculator is actually a shape or if the shape has a method named area?

- Coding to an interface is an integral part of SOLID.

- Create a ShapeInterface that supports the area:

```
abstract class ShapeInterface {
   double area();
}
```

- Modify your shape classes to implement the ShapeInterface.

- Here is the update to Square:

```
class Square implements ShapeInterface
{
    // ...
}
```

- And here is the update to Circle:

```
class Circle implements ShapeInterface
{
    // ...
}
```

- In the sum method for AreaCalculator, to avoid runtime exceptions because of the type, we can change
List<dynamic> to List<ShapeInterface>

```
class AreaCalculator {
  List<ShapeInterface> shapes; // before change => List<dynamic> shapes;
 
  double sum() {
    double area = 0.0;

    shapes.forEach((item) {
        area += item.area();
        
    return area;
    }
  }
  ```
 
## **L - Liskov Substitution Principle**
**L - Liskov Substitution Principle** states that if q(x) is a property provable about objects of x of type T. Then q(y) should be provable for objects y of type S where S is a subtype of T.

- This means that every subclass or derived class should be substitutable for its base or parent class.

- Building off the example AreaCalculator class, consider a new VolumeCalculator class that extends the AreaCalculator class:

```
class VolumeCalculator extends AreaCalculator
{
    VolumeCalculator( List<dynamic> shapesshapes ):super(shapesshapes);
    

    dynamic sum()
    {
        var summedData;
        // logic to calculate the volumes and then return an array of output
        return summedData;
    }
}
```

- The summedData can be a float, double, or integer.

- Recall that the SumCalculatorOutputter class. If you tried to run an example like this:

```
areas =  AreaCalculator(shapes);
volumes =  VolumeCalculator(solidShapes);

output =  SumCalculatorOutputter(areas);
output2 =  SumCalculatorOutputter(volumes);
```

- When you call the HTML method on the $output2 object, you will get an E_NOTICE error informing you of an array to string conversion.

- That satisfies the Liskov substitution principle.

 
## **I - Interface Segregation Principle**
**I - Interface Segregation Principle** states that a client should not be forced to implement an interface that it doesn’t use, or clients shouldn’t be forced to depend on methods they do not use.

- Still building from the previous ShapeInterface example, you will need to support the new three-dimensional shapes of Cuboid and Spheroid, and these shapes will need to also calculate volume.

- Let’s consider what would happen if you were to modify the ShapeInterface to add another contract:

```
abstract class ShapeInterface
{
    double area();
    double volume();
}
```

- Now, any shape you create must implement the volume method, but you know that squares are flat shapes and that they do not have volumes, so this interface would force the Square class to implement a method that it has no use of.

- This would violate the interface segregation principle. Instead, you could create another interface called ThreeDimensionalShapeInterface that has the volume contract and three-dimensional shapes can implement this interface:

```
abstract class ShapeInterface
{
    double area();
}

abstract class ThreeDimensionalShapeInterface
{
    double volume();
}

class Cuboid implements ShapeInterface, ThreeDimensionalShapeInterface
{
    double area()
    {
        // calculate the surface area of the cuboid
    }

    double volume()
    {
        // calculate the volume of the cuboid
    }
}
```

- This is a much better approach, but a pitfall to watch out for is when type-hinting these interfaces. Instead of using a ShapeInterface or a ThreeDimensionalShapeInterface, you can create another interface, maybe ManageShapeInterface, and implement it on both the flat and three-dimensional shapes.

- This way, you can have a single API for managing the shapes:

```
abstract class ManageShapeInterface
{
    double calculate();
}

class Square implements ShapeInterface, ManageShapeInterface
{
    double area()
    {
        // calculate the area of the square
    }

    double calculate()
    {
        return this.area();
    }
}

class Cuboid implements ShapeInterface, ThreeDimensionalShapeInterface, ManageShapeInterface
{
    double area()
    {
        // calculate the surface area of the cuboid
    }

   double volume()
    {
        // calculate the volume of the cuboid
    }

    double calculate()
    {
        return this.area();
    }
}
```

- Now in AreaCalculator class, you can replace the call to the area method with calculate and also check if the object is an instance of the ManageShapeInterface and not the ShapeInterface.

- That satisfies the interface segregation principle.

## **D - Dependency Inversion Principle**
**D - Dependency Inversion Principle** states that Entities must depend on abstractions, not on concretions. It states that the high-level module must not depend on the low-level module, but they should depend on abstractions.

- This principle allows for decoupling.

- Here is an example of a PasswordReminder that connects to a MySQL database:

```
class MySQLConnection
{
    String connect()
    {
        // handle the database connection
        return 'Database connection';
    }
}

class PasswordReminder
{
    MySQLConnection _dbConnection;

    PasswordReminder(MySQLConnection dbConnection)
    {
        this.dbConnection = dbConnection;
    }
}
```

- First, the MySQLConnection is the low-level module while the PasswordReminder is high level, but according to the definition of D in SOLID, which states to Depend on abstraction, not on concretions. This snippet above violates this principle as the PasswordReminder class is being forced to depend on the MySQLConnection class.

- Later, if you were to change the database engine, you would also have to edit the PasswordReminder class, and this would violate the open-close principle.

- The PasswordReminder class should not care what database your application uses. To address these issues, you can code to an interface since high-level and low-level modules should depend on abstraction:

```
abstract class DBConnectionInterface
{
    String connect();
}
```

- The interface has a connect method and the MySQLConnection class implements this interface. Also, instead of directly type-hinting MySQLConnection class in the constructor of the PasswordReminder, you instead type-hint the DBConnectionInterface, and no matter the type of database your application uses, the PasswordReminder class can connect to the database without any problems and open-close principle is not violated.

```
class MySQLConnection implements DBConnectionInterface
{
    String connect()
    {
        // handle the database connection
        return 'Database connection';
    }
}

class PasswordReminder
{
    MySQLConnection _dbConnection;

    PasswordReminder(DBConnectionInterface dbConnection)
    {
        this.dbConnection = dbConnection;
    }
}
```

This code establishes that both the high-level and low-level modules depend on abstraction.