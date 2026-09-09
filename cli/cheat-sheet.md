# Dart Cheat sheet

## Init dart project

`dart run` creates a new dart project
`dart bin/name-of-file.dart argument` execute a dart files (bin/name-of-file.dart) with arguments (argument)

## Control flow

`arguments.isEmpty` checks if no command-line arguments were provided.
`arguments.first` accesses the very first argument, which you're using as our command.
`version` is declared as a const. This means its value is known at compile time, and you can't change it during runtime.
`arguments` is a regular (non-constant) variable because its content can change during runtime based on user input.

## Null statement

`List<String>?` arguments means that the `arguments` list itself can be null.
Dart enforces sound null safety, which means you have to explicitly state when a variable can be null. Any variable that isn't marked as nullable is guaranteed to never be null, even in production.

`final` variables can only be set once and are used when you never intend to change the variable again in the code.
`arguments.sublist(1)` creates a new list containing all elements of the `arguments` list after the first element (which was search).
`arguments.length > 1 ? ... : null;` is a conditional (ternary) operator. It ensures that if no arguments are provided after the search command, inputArgs becomes `null`, matching the sample code's behavior for `searchWikipedia`'s `arguments` parameter of `List<String>?`.

`dart:io` is a core library in the Dart SDK, and provides APIs to deal with **files, directories, sockets, and HTTP clients and servers**, and more.

`stdin.readLineSync() ?? ''` reads the input from the user. While `stdin.readLineSync()` can return `null`, the null-coalescing operator (`??`) is used to provide an empty string ('') as a fallback if the input is `null`. This is a concise way to ensure that the variable is a non-null string.
`arguments.join(' ')` concatenates all elements of the arguments list into a single string, using a space as the separator. For example, `['Dart', 'Programming']` becomes "Dart Programming". This is crucial for treating multi-word command-line inputs as a single search phrase.
`??` the null-coalescing operator (`??`) is used to provide an empty string ('') as a fallback if the input is null. This is a concise way to ensure that the variable is a non-null string.

## Dependencies

`projectName/pubspec.yaml` is Dart's project metadata (like package.json)
`dart pub get` Add new dependencies

## Asynchronous operations

The `Future<String>` return type indicates that this function will eventually produce a String result, but not immediately, because it's an asynchronous operation.
The `async` keyword marks the function as asynchronous, allowing you to use await inside it.

You learned that Future represents a value that will be available later. The async keyword marks functions as asynchronous, and await pauses execution until a Future completes.
You modified pubspec.yaml to add the http package, ran dart pub get to fetch it, and imported it with an alias (as http). You can follow these steps to add any external package from pub.dev.
You built getWikipediaArticle() to make HTTP GET requests using the get function from package:http, constructed URIs, checked status codes, and returned the response body. Your CLI now fetches real data from the web!

## Fetch data

```dart
Future<String> getWikipediaArticle(String articleTitle) async {
  final url = Uri.https(
    'en.wikipedia.org',
    'api/rest_v1/page/summary/$articleTitle',
  );
  final response = await http.get(url);
  if (response.statusCode == 200) {
    return response.body;
  } else {
    throw Exception('Failed to load article');
  }
}
```

## Pub packages

`library`; declares this file as a library, which defines the boundaries and public interface of a reusable unit of Dart code.
`export 'src/command_runner_base.dart';` is a crucial line that makes declarations from `command_runner_base.dart` available to other packages that import the `command_runner` package. Without this export statement, the classes and functions within `command_runner_base.dart` would be private to the command_runner package, and you wouldn't be able to use them in your dartpedia application.
You used `dart create -t package command_runner` to scaffold a new library package intended to be used in other packages. Library packages are the standard way to organize and share reusable Dart libraries, both within a project workspace or published to pub.dev.
You added `export` statements in your library file to expose specific libraries and their definitions to consumers. If not exported from files in the `lib` root directory, code in `lib/src/` remains private to the package. This allows you to carefully craft a public API for others, while still having full control over the implementation details.
You configured `pubspec.yaml` to depend on a local package using a path dependency. This enables multi-package projects where packages can evolve together before being published separately.

## OOP

You created an abstract Argument class as a base class that can't be instantiated directly. Abstract classes define a contract that subclasses must fulfill, ensuring consistency across your class hierarchy.

You used extends to create Option and Command subclasses of the abstract Argument class. Within those subclasses, you used @override to provide concrete implementations of abstract members.

You defined an OptionType enum to represent a fixed set of values. In Dart, enums are type-safe and help prevent invalid values from being used where only specific options are valid.

You applied object-oriented programming principles to implement a robust and extensible framework for parsing command-line arguments.

When should you use an enum instead of a class or a set of constants?
When you need a type that can only be one of a fixed, known set of values.
Enums are perfect when you have a closed set of options, like flag versus option, days of the week, or status codes. The compiler ensures you handle all cases.

What is the difference between an abstract class and a regular class in Dart?
Abstract classes serve as blueprints that other classes extend. You can't create instances of an abstract class directly.

In the Option class, what is the purpose of the @override annotation?
To provide a specific implementation for a method or property defined in a parent class.
@override indicates that you're providing a concrete implementation for an abstract member or replacing an inherited implementation.

## Errors versus exceptions

`Exceptions` represent conditions that you might expect to happen and that your program can recover from. Examples include invalid user input, a missing file, or a network timeout. Your code can catch and handle exceptions to keep running.
`Errors` represent failure conditions that indicate bugs in the code. Examples include calling a method on a null object, passing an invalid index to a list, or failing to initialize a late variable before use. You don't catch errors. Instead, you let them crash the application, which helps you find and fix the underlying bug.

Distinguished errors from exceptions: Subtypes of Error indicate programming bugs and aren't intended to be caught, while subtypes of Exception represent recoverable failures that your code can handle gracefully.

`Try-catch` block to handle failures: You wrapped risky code in `try-catch` blocks to intercept `exceptions`. You used on `ExceptionType` catch (e) to handle specific types and rethrow to re-propagate exceptions while preserving the stack trace.

Created and threw custom exceptions: You built a `ArgumentException` class that extends `FormatException` to provide context-rich error information. Then you used `throw` to signal validation failures with meaningful messages for better debugging and user feedback.

You're writing a function that parses user input. If the input is invalid, what's the best way to signal this to the calling code?
Throw an exception describing what went wrong.
Throwing an exception immediately stops the invalid code path, provides a clear error message, and forces the caller to handle the error explicitly.

What's the difference between throw and rethrow in a catch block?
throw always creates a new stack trace; rethrow preserves the original stack trace.
Use rethrow when you want to log or partially handle an exception but still let it propagate with its original stack trace intact for debugging.

Consider this code: try { riskyOperation(); } on FormatException catch (e) { print(e); }. What happens if riskyOperation() throws an HttpException?
The HttpException propagates up, uncaught by this try/catch.
The on clause filters by exception type. Since HttpException isn't a FormatException, it bypasses this catch block entirely.

## Console color

- You created ConsoleColor as an enhanced enum with r, g, b fields and methods like applyForeground(). Enhanced enums can have constructors, properties, and methods, combining the benefits of enums and normal classes.
- You created a TextRenderUtils extension on the String class to add getters like errorText and titleText to all strings. Extensions let you add functionality to any type without needing to modify or subclass it, which is particularly useful in cases like strings which can't be subclassed.
- To improve the readability and user experience of your CLI, you used ANSI escape codes to style terminal output with colors. In particular, you used ConsoleColor and updated your CLI to display errors in red, titles in blue, and instructions in yellow.

What is an enhanced enum in Dart?
An enum that can have methods and properties.
Enhanced enums allow you to add fields, constructors, and methods to enum types, making them much more powerful than simple enumerations.

What is a Dart extension?
A way to add new methods to existing classes.
Extensions allow you to add new functionality to existing types without modifying them or creating subclasses.

You want to add a `capitalizeWords()` method to String, but you can't modify the String class. What's the best approach?
Create an extension on String that adds the method.
Extensions let you add methods to existing types. You can then call 'hello world'.capitalizeWords() as if capitalizeWords was built into String.

## String buffer and refactored

You replaced string concatenation with a StringBuffer, which can be more efficient for building strings in loops. Methods like writeln() and write() append content, while toString() produces the final string.

You improved the help system to show detailed usage information including options, default values, and descriptions. The --verbose flag and --command option give users control over the level of detail.

You added onOutput to CommandRunner, allowing library consumers to customize how output is displayed. This pattern makes your package more flexible and adaptable, enabling delayed printing, logging, or output redirection.

What is the purpose of the StringBuffer class in Dart?
To efficiently build strings by appending multiple parts.
StringBuffer provides efficient string concatenation by avoiding the creation of intermediate string objects during multiple, successive appends.

What does the onOutput argument in the CommandRunner class allow you to do?
Customize the output handling of a command.
onOutput lets you define custom behavior for command output, such as formatting, logging, or writing to different destinations.
