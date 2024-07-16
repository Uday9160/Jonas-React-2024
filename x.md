- **Creating a C# Class**

  - Use the `class` keyword followed by the class name.
  - No semicolons after the class declaration.
  - Class names should start with a capital letter by convention.

  ```csharp
  public class Rectangle
  {
      // Class definition
  }
  ```

- **Fields of a Class**

  - Fields are variables that belong to an object of a class.
  - Each instance of the class can have different values for its fields.
  - Example for `Rectangle` class with `width` and `height` fields:

  ```csharp
  public class Rectangle
  {
      public int width;
      public int height;
  }
  ```

- **Creating an Object**

  - Instantiate an object using the `new` keyword.
  - Default parameterless constructor is used if no custom constructor is defined.

  ```csharp
  Rectangle myRectangle = new Rectangle();
  ```

- **Default Constructor**

  - Automatically created if no constructors are defined.
  - Initializes fields to their default values (e.g., `int` fields to `0`).

- **Field Initialization**

  - Fields are automatically initialized to their default values if not explicitly set.
  - Example:

  ```csharp
  Rectangle rect = new Rectangle();
  Console.WriteLine(rect.width);  // Output: 0
  Console.WriteLine(rect.height); // Output: 0
  ```

- **Compilation Errors and Field Accessibility**
  - Attempting to use uninitialized variables results in a compilation error.
  - Fields have default values if not initialized: integers default to `0`.
  - Accessing fields with insufficient protection levels results in compilation errors.

[End of Notes]
