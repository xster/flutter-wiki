Status: draft, non-binding while in development.

## Purpose

This document describes the style guide for Flutter code (Dart code that runs in Flutter) that is embedded into the [flutter.io](https://flutter.io) web pages. Dart code that is displayed in web pages on flutter.io should comply with this style guide.

## Rationale

Code that is displayed inside of flutter.io documentation is intended to serve as examples of how _application developers_ would write their own Flutter code, while balancing terseness for the purposes of documentation flow and illustrative purposes, as well as helping to make Flutter appear modern for mobile developers.

Flutter uses [strong mode][strongmode] to deliver a sound type system, which can provide some type inferencing. Application developers can rely on type inference to provide types for variables, without having to explicitly write the types in their code. Code for flutter.io's documentation can take advantage of this feature for decreased verbosity and increased appeal by modern mobile developers.

Because the [Flutter repo's style guide][repo-style] has different goals and a different audience (developers writing code for the Flutter repo), we created this style guide as an "amendment" to the Flutter Repo style guide.

## Style guide

Code that is displayed on [flutter.io][] should follow the [Flutter repo style guide][repo-style], unless contradicted by this document.

### MUST: Formatting

Use `dartfmt` to format the code. Application developers have the `dartfmt` tool, and thus the code they see on the website should look like the code they will write when building their own applications.

Use _trailing commas_ to generate easier-to-read `build()` methods. To learn more about trailing commas and formatting Flutter code, see [https://flutter.io/formatting/][].

The website's Travis CI jobs can enforce styles by using the `dartfmt` tool during a website build.

### SHOULD: Use var when type can be inferred

Flutter uses _strong mode_ (a more sound type system than _optional types_), which implements _type inferencing_. Flutter application developers are free and encouraged to use `var` when the type can easily be inferred.

For example, use `var` inside of a function or method when also initializing the variable:

```dart
String appendMessage(String message) {
  var appendWith = '!!!';  // here, appendWith is inferred to be a String
  return "$message $appendWith";
}
```

TODO: link to documentation for Dart Strong Mode's type inference.

### SHOULD: Don't use generic annotations for collection literals when all items are of the same type

Dart's strong mode's type inference will infer the parameterized type of a collection literal when all items in the collection literal are of the same type (and not null).

Instead of this:

```dart
// BAD: using <String> here
var fruits = <String>['apples', 'bananas', 'strawberries'];
```

Please do this:

```dart
// GOOD: relying on type inference, fruits is still a "list of strings"
var fruits = ['apples', 'bananas', 'strawberries'];
```

## See also

* [Style guide for Flutter repo][repo-style]
* [Effective Dart: Usage](https://www.dartlang.org/guides/language/effective-dart/usage)


[repo-style]: https://github.com/flutter/flutter/wiki/Style-guide-for-Flutter-repo
[strongmode]: https://www.dartlang.org/guides/language/sound-dart