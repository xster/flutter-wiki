Introduction
------------

_Please also read our [Design Principles](https://flutter.io/design-principles/) which
includes our code review philosophy and our code of conduct._

This style guide describes the preferred style for code written as part of the Flutter
project (the framework itself and all our sample code). Flutter application developers
are welcome to follow this style as well, but this is by no means required. Flutter
will work regardless of what style is used to author applications that use it.

Overview
--------

The primary goal of these style guidelines is to improve code readability so
that everyone, whether reading the code for the first time or
maintaining it for years, can quickly determine what the code does.
Secondary goals are to increase the likelihood of catching bugs quickly, and
avoiding arguments when there are disagreements.

In general, when writing code for the Flutter repository, follow our
[Design Principles](https://flutter.io/design-principles/) for all code and
the [Dart style guide](https://www.dartlang.org/guides/language/effective-dart/)
for Dart code, except where that would contradict this page.

We do not yet use `dartfmt`. Flutter code tends to use patterns that
the standard Dart formatter does not handle well. We are
[working with the Dart team](https://github.com/dart-lang/dart_style/issues/442)
to make `dartfmt` aware of these patterns.


In defense of the extra work that hand formatting entails
---------------------------------------------------------

Flutter code might eventually be read by hundreds of thousands of people each day.
Code that is easier to read and understand saves these people time. Saving each
person even a second each day translates into hours or even _days_ of saved time
each day. The extra time spent by people contributing to Flutter directly translates
into real savings for our developers, which translates to real benefits to our end
users as our developers learn the framework faster.


Comments
--------

### Avoid checking in commented-out code

It will bitrot too fast to be useful, and will confuse people maintaining the
code.


### Avoid checking in comments that ask questions

Find the answers to the questions, or describe the confusion, including
references to where you found answers.

If commenting on a workaround due to a bug, also leave a link to the bug and
a TODO to clean it up when the bug is fixed.

Example:

```
// BAD:

// What should this be?

// This is a workaround.


// GOOD:

// According to this specification, this should be 2.0, but according to that
// specification, it should be 3.0. We split the difference and went with
// 2.5, because we didn't know what else to do.

// TODO(username): Converting color to RGB because class Color doesn't support
//                 hex yet. See http://link/to/a/bug/123
```


Documentation comments (dartdocs)
---------------------------------

We use "dartdoc" for our documentation. All public members in Flutter
libraries should have a dartdoc comment, consisting of three slashes
(rather than two slashes as used for regular comments).

In general, follow the
[Dart documentation guide](https://www.dartlang.org/effective-dart/documentation/#doc-comments)
except where that would contradict this page.

### Avoid useless documentation

If someone could have written the same documentation without knowing
anything about the class other than its name, then it's useless.

Avoid checking in such documentation, because it is no better than no
documentation but will prevent us from noticing that the identifier is
not actually documented.

Example (from [`CircleAvatar`](http://docs.flutter.io/flutter/material/CircleAvatar-class.html)):

<!-- skip -->
```dart
// BAD:

/// The background color.
final Color backgroundColor;

/// Half the diameter of the circle.
final double radius;


// GOOD:

/// The color with which to fill the circle. Changing the background
/// color will cause the avatar to animate to the new color.
final Color backgroundColor;

/// The size of the avatar. Changing the radius will cause the
/// avatar to animate to the new size.
final double radius;
```

### Avoid empty prose

It's easy to use more words than necessary. Avoid doing so
where possible, even if the result is somewhat terse.

```
// BAD:

/// Note: It is important to be aware of the fact that in the
/// absence of an explicit value, this property defaults to 2.

// GOOD:

/// Defaults to 2.
```

In particular, avoid saying "Note:". It adds nothing.

### Leave breadcrumbs in the comments

This is especially important for documentation at the level of classes.

If a class is constructed using a builder of some sort, or can be
obtained via some mechanism other than merely calling the constructor,
then include this information in the documentation for the class.

If a class is typically used by passing it to a particular API, then
include that information in the class documentation also.

If a method is the main mechanism used to obtain a particular object,
or is the main way to consume a particular object, then mention that
in the method's description.

Typedefs should mention at least one place where the signature is used.

These rules result in a chain of breadcrumbs that a reader can follow
to get from any class or method that they might think is relevant to
their task all the way up to the class or method they actually need.

Example:

<!-- skip -->
```dart
// GOOD:

/// An object representing a sequence of recorded graphical operations.
///
/// To create a [Picture], use a [PictureRecorder].
///
/// A [Picture] can be placed in a [Scene] using a [SceneBuilder], via
/// the [SceneBuilder.addPicture] method. A [Picture] can also be
/// drawn into a [Canvas], using the [Canvas.drawPicture] method.
abstract class Picture ...
```

You can also use "See also" links, is in:

```
/// See also:
///
/// * [FooBar].
/// * [Baz], which quuxes the wibble.
```
Each line should end with a period. Prefer "which..." rather than parentheticals on such lines.

### Refactor the code when the documentation would be incomprehensible

If writing the documentation proves to be difficult because the API is
convoluted, then rewrite the API rather than trying to document it.


### If you have a question, put the answer in the documentation.

Put the answer to your question where you went to look for the answer. That way, the
documentation will consist of answers to real questions, where people would look to
find them. Do this right away; it's fine if your otherwise-unrelated PR has a bunch of
documentation fixes in it to answer questions you had while you were working on your PR.


### Canonical terminology

The documentation should use consistent terminology:

 * _method_ - a member of a class that is a non-anonymous closure
 * _function_ - a callable non-anonymous closure that isn't a member of a class
 * _parameter_ - the variable used in a method body.
 * _argument_ - the value passed to a method call.

Typedef dartdocs should usually start with the phrase "Signature for...".

### Provide sample code

Sample code helps developers learn your API quickly. Writing sample code also helps you think through how your API is going to be used by app developers.

Sample code should go in a section of the documentation that has a `## Sample code` header. This will then be checked by automated tools.

For example, below is the sample code for building an infinite list of children with the ListView widget:

```dart
new ListView.builder(
  padding: new EdgeInsets.all(8.0),
  itemExtent: 20.0,
  itemBuilder: (BuildContext context, int index) {
    return new Text('entry $index');
  },
) 
```

### Provide illustrations, diagrams or screenshots

For any widget that draws pixels on the screen, showing how it looks like in its API doc helps developers decide if the widget is useful and learn how to customize it. All illustrations should be easily reproducible, e.g. by running a Flutter app or a script.

Examples:

* A diagram for the AppBar widget

![image](https://flutter.github.io/assets-for-api-docs/material/app_bar.png)


* A screenshot for the Card widget

![image](https://user-images.githubusercontent.com/348942/28338544-2c3681b8-6bbe-11e7-967d-fcd7c830bf53.png)

### Clearly mark deprecated APIs

According to Flutter's [Design Principles](https://flutter.io/design-principles/#handling-breaking-changes),
use `@deprecated` with a clear
recommendation of what to use instead.

In some cases, using `@deprecated` will turn the tree red for longer than the Flutter team
can accommodate. In those cases, and when we want to give developers enough time to
move to the new API, you should use this format:

```dart
// GOOD

/// (Deprecated, use [lib.class] instead) Original one-line statement.
///
/// A longer, one-liner that explains the context for the deprecation.
///
/// The rest of the comments
```

Coding patterns and catching bugs early
---------------------------------------

### Run the Dart Analyzer before submitting code

While editing code using IntelliJ with [our plugins](https://flutter.io/intellij-ide/), the 
Analyzer runs automatically in the background, preventing surprises later when you need 
to submit the code.

Run `flutter analyze --flutter-repo` prior to submitting your code for review.

Avoid checking in code that increases the output of the analyzer. If a warning
must be allowed due to a bug in the analyzer file a bug with the Dart team at
<http://dartbug.com/new>.


### Use asserts liberally to enforce contracts and invariants

`assert()` allows us to be diligent about correctness without paying a
performance penalty in release mode, because Dart only evaluates asserts in
checked mode.

The following example is from `box.dart`:

<!--
dynamic _debugDoingBaseline;
-->
```dart
abstract class RenderBox extends RenderObject {
  // ...

  double getDistanceToBaseline(TextBaseline baseline, {bool onlyReal: false}) {
    // simple asserts:
    assert(!needsLayout);
    assert(!_debugDoingBaseline);
    // more complicated asserts:
    assert(() {
      final RenderObject parent = this.parent;
      if (owner.debugDoingLayout)
        return (RenderObject.debugActiveLayout == parent) &&
            parent.debugDoingThisLayout;
      if (owner.debugDoingPaint)
        return ((RenderObject.debugActivePaint == parent) &&
                parent.debugDoingThisPaint) ||
            ((RenderObject.debugActivePaint == this) && debugDoingThisPaint);
      assert(parent == this.parent);
      return false;
    });
    // ...
    return 0.0;
  }

  // ...
}
```


### Avoid using "if" chains on enum values

Use `switch` if you are examining an enum (and avoid using `if` chains
with enums), since the analyzer will warn you if you missed any of the
values when you use `switch`.

Similarly, avoid using `? ... : ...` with expressions involving enums.


### Prefer specialized functions, methods and constructors

Use the most relevant constructor or method, when there are multiple
options.

Example:

<!-- skip -->
```dart
// BAD:
new EdgeInsets.TRBL(0.0, 8.0, 0.0, 8.0);

// GOOD:
new EdgeInsets.symmetric(horizontal: 8.0);
```


### Perform dirty checks in setters

When defining mutable properties that mark a class dirty when set, use
the following pattern:

<!-- skip -->
```dart
/// Documentation here (don't wait for a later commit).
TheType get theProperty => _theProperty;
TheType _theProperty;
void set theProperty(TheType value) {
  assert(value != null);
  if (_theProperty == value)
    return;
  _theProperty = value;
  markNeedsWhatever(); // the method to mark the object dirty
}
```

The argument is called 'value' for ease of copy-and-paste reuse of
this pattern. If for some reason you don't want to use 'value', use
'newTheProperty' (where 'theProperty' is the property name).

Start the method with any asserts you need to validate the value.


### Minimize the visibility scope of constants

Prefer using a local const or a static const in a relevant class than using a
global constant.

### Avoid using "var"

All variables and arguments are typed; avoid "dynamic" or "Object" in
any case where you could figure out the actual type. Always specialize
generic types where possible. Explicitly type all list and map
literals.

Always avoid "var". Use "dynamic" if you are being explicit that the
type is unknown. Use "Object" if you are being explicit that you want
an object that implements `==` and `hashCode`.

Avoid using "as". If you know the type is correct, use an assertion or
assign to a more narrowly-typed variable (this avoids the type check
in release mode; "as" is not compiled out in release mode). If you
don't know whether the type is correct, check using "is" (this avoids
the exception that "as" raises).


Naming
------

### Begin global constant names with prefix "k"

Examples:

```dart
const double kParagraphSpacing = 1.5;
const String kSaveButtonTitle = 'Save';
const Color _kBarrierColor = Colors.black54;
```

However, where possible avoid global constants. Rather than `kDefaultButtonColor`, consider `Button.defaultColor`. If necessary, consider creating a class with a private constructor to hold relevant constants.


### Naming rules for typedefs and function variables

When naming callbacks, use `FooCallback` for the typedef, `onFoo` for
the callback argument or property, and `handleFoo` for the method
that is called.

If you have a callback with arguments but you want to ignore the
arguments, name them `_`, `__`, `___`, etc. If you name any of them,
name all of them. Always be explicit with the types of variables in
callbacks unless you are ignoring them (and have named them with
underscores).


### Spell words in identifiers and comments correctly

Our primary source of truth for spelling is the
[Material Design Specification](https://material.google.com/).
Our secondary source of truth is dictionaries.

Avoid "cute" spellings. For example, 'colors', not 'colorz'.

Prefer US English spellings. For example, 'colorize', not 'colourise'.


### Capitalize identifiers consistent with their spelling

If a word is correctly spelt (according to our sources of truth as described in the previous section) as a single word, then it should not have any inner capitalization or spaces.

For examples, prefer `toolbar`, `scrollbar`, but `appBar` ('app bar' in documentation), `tabBar` ('tab bar' in documentation).

Similarly, prefer `offstage` rather than 'offStage`.


### Avoid double negatives in APIs

Name your boolean variables in positive ways, such as "enabled" or "visible", even if the default value is true.

This is because, when you have a property or argument named "disabled" or "hidden", it leads to code such as `input.disabled = false` or `widget.hidden = false` when you're trying to enable or show the widget, which is very confusing.


### Prefer naming the argument to a setter `value`

Unless this would cause other problems, use `value` for the name of a setter's argument. This makes it easier to copy/paste the setter later.


### Qualify variables used only for debugging

If you have variables or methods that are only used in checked mode,
prefix their names with `debug` or `_debug`.

Do not use debugging variables in production code.


### Avoid naming undocumented libraries

In other words, do not use the `library` keyword, unless it is a
documented top-level library intended to be imported by users.


Formatting
----------

These guidelines have no technical effect, but they are still important purely
for consistency and readability reasons.

### Constructors come first in a class

The default (unnamed) constructor should come first, then the named
constructors. They should come before anything else (including, e.g., constants or static methods).

This helps readers determine whether the class has a default implied constructor or not at a glance. If it was possible for a constructor to be anywhere in the class, then the reader would have to examine every line of the class to determine whether or not there was an implicit constructor or not.


### Order other class members in a way that makes sense

The methods, properties, and other members of a class should be in an order that
will help readers understand how the class works.

If there's a clear lifecycle, then the order in which methods get invoked would be useful, for example an  `initState` method coming before `dispose`. This helps readers because the code is in chronological order, so
they can see variables get initialized before they are used, for instance. Fields should come before the methods that manipulate them, if they are specific to a particular group of methods.

> For example, RenderObject groups all the layout fields and layout
> methods together, then all the paint fields and paint methods, because layout
> happens before paint.

If no particular order is obvious, then the following order is suggested, with blank lines between each one:

1. Constructors, with the default constructor first.
2. Constants of the same type as the class.
3. Static methods that return the same type as the class.
4. Final fields that are set from the constructor.
5. Other static methods.
6. Static properties and constants.
7. Mutable properties, each in the order getter, private field, setter, without newlines separating them.
8. Read-only properties (other than `hashCode`).
9. Operators (other than `==`).
10. Methods (other than `toString` and `build`).
11. The `build` method, for `Widget` and `State` classes.
12. `operator ==`, `hashCode`, `toString`, and diagnostics-related methods, in that order.

Be consistent in the order of members. If a constructor lists multiple
fields, then those fields should be declared in the same order, and
any code that operates on all of them should operate on them in the
same order (unless the order matters).


### Constructor syntax

If you call `super()` in your initializer list, put a space between the
constructor arguments' closing parenthesis and the colon. If there's
other things in the initializer list, align the `super()` call with the
other arguments. Don't call `super` if you have no arguments to pass up
to the superclass.

```dart
// one-line constructor example
abstract class Foo extends StatelessWidget {
  Foo(this.bar, { Key key, this.child }) : super(key: key);
  final int bar;
  final Widget child;
  // ...
}

// fully expanded constructor example
abstract class Foo extends StatelessWidget {
  Foo(
    this.bar, {
    Key key,
    Widget childWidget,
  }) : child = childWidget,
       super(
         key: key,
       );
  final int bar;
  final Widget child;
  // ...
}
```


### Prefer line length of 80 characters

Aim for a line length of 80 characters, but go over if breaking the
line would make it less readable. When wrapping lines, avoid doing so
around assignment operators. Indent the next line by two characters
or align the expressions, whichever makes the code more readable.


### Indent multi-line argument and parameter lists by 2 characters

When breaking an argument list into multiple lines, indent the
arguments two characters from the previous line.

Example:

<!-- skip -->
```dart
Foo f = new Foo(
  bar: 1.0,
  quux: 2.0,
);
```

When breaking a parameter list into multiple lines, do the same.


### If you have a newline after some opening punctuation, match it on the closing punctuation.

And vice versa.

Example:

<!-- skip -->
```dart
// BAD:
  foo(
    bar, baz);
  foo(
    bar,
    baz);
  foo(bar,
    baz
  );

// GOOD:
  foo(bar, baz);
  foo(
    bar,
    baz,
  );
  foo(bar,
    baz);
```

### Use a trailing comma for arguments, parameters, and list items, but only if they each have their own line.

Example:
```dart
List<int> myList = [
  1,
  2,
]
myList = <int>[3, 4];

foo1(
  bar,
  baz,
);
foo2(bar, baz);
```

### Prefer single quotes for strings

Use double quotes for nested strings.

Example:

<!-- skip -->
```dart
print('Hello ${name.split(" ")[0]}');
```


### Consider using "=>" for short functions and methods

But only use `=>` when everything, including the function declaration, fits
on a single line.

Example:

<!-- skip -->
```dart
// BAD:
String capitalize(String s) =>
  '${s[0].toUpperCase()}${s.substring(1)}';

// GOOD:
String capitalize(String s) => '${s[0].toUpperCase()}${s.substring(1)}';

String capitalize(String s) {
  return '${s[0].toUpperCase()}${s.substring(1)}';
}
```

### Use `=>` for inline callbacks that just return list or map literals

If your code is passing an inline closure that merely returns a list or
map literal, or is merely calling another function, then if the argument
is on its own line, then rather than using braces and a `return` statement,
you can instead use the `=>` form. When doing this, the closing `]`, `}`, or
`)` bracket will line up with the argument name, for named arguments, or the
`(` of the argument list, for positional arguments.

For example:

<!-- skip -->
```dart
    // GOOD, but slightly more verbose than necessary since it doesn't use =>
    @override
    Widget build(BuildContext context) {
      return new PopupMenuButton<String>(
        onSelected: (String value) { print('Selected: $value'); },
        itemBuilder: (BuildContext context) {
          return <PopupMenuItem<String>>[
            new PopupMenuItem<String>(
              value: 'Friends',
              child: new MenuItemWithIcon(Icons.people, 'Friends', '5 new')
            ),
            new PopupMenuItem<String>(
              value: 'Events',
              child: new MenuItemWithIcon(Icons.event, 'Events', '12 upcoming')
            ),
          ];
        }
      );
    }

    // GOOD, does use =>, slightly briefer
    @override
    Widget build(BuildContext context) {
      return new PopupMenuButton<String>(
        onSelected: (String value) { print('Selected: $value'); },
        itemBuilder: (BuildContext context) => <PopupMenuItem<String>>[
          new PopupMenuItem<String>(
            value: 'Friends',
            child: new MenuItemWithIcon(Icons.people, 'Friends', '5 new')
          ),
          new PopupMenuItem<String>(
            value: 'Events',
            child: new MenuItemWithIcon(Icons.event, 'Events', '12 upcoming')
          ),
        ]
      );
    }
```

The important part is that the closing punctuation lines up with the start
of the line that has the opening punctuation, so that you can easily determine
what's going on by just scanning the indentation on the left edge.


### Use braces for long functions and methods

Use a block (with braces) when a body would wrap onto more than one line (as opposed to using `=>`; the cases where you can use `=>` are discussed in the previous two guidelines).


### Separate the 'if' expression from its statement

Don't put the statement part of an 'if' statement on the same line as
the expression, even if it is short. (Doing so makes it unobvious that
there is relevant code there. This is especially important for early
returns.)

Example:

<!-- skip -->
```dart
// BAD:
if (notReady) return;

// GOOD:
if (notReady)
  return;

// ALSO GOOD:
if (notReady) {
  return;
}
```


### Align expressions

Where possible, subexpressions on different lines should be aligned, to make the structure of the expression easier. When doing this with a `return` statement chaining `||` or `&&` operators, consider putting the operators on the left hand side instead of the right hand side.

```dart
// BAD:
if (foo.foo.foo + bar.bar.bar * baz - foo.foo.foo * 2 +
    bar.bar.bar * 2 * baz > foo.foo.foo) {
  // ...
}

// GOOD (notice how it makes it obvious that this code can be simplified):
if (foo.foo.foo +     bar.bar.bar *     baz -
    foo.foo.foo * 2 + bar.bar.bar * 2 * baz   > foo.foo.foo) {
  // ...
}
// After simplification, it fits on one line anyway:
if (bar.bar.bar * 3 * baz > 2 * foo.foo.foo) {
  // ...
}
```

```dart
// BAD:
return foo.x == x &&
    foo.y == y &&
    foo.z == z;

// GOOD:
return foo.x == x &&
       foo.y == y &&
       foo.z == z;

// ALSO GOOD:
return foo.x == x
    && foo.y == y
    && foo.z == z;
```


Packages
--------

As per normal Dart conventions, a package should have a single import
that reexports all of its API.

> For example,
> [rendering.dart](https://github.com/flutter/engine/blob/master/sky/packages/sky/lib/rendering.dart)
> exports all of lib/src/rendering/*.dart

If a package uses, as part of its exposed API, types that it imports
from a lower layer, it should reexport those types.

> For example,
> [material.dart](https://github.com/flutter/engine/blob/master/sky/packages/sky/lib/material.dart)
> reexports everything from
> [widgets.dart](https://github.com/flutter/engine/blob/master/sky/packages/sky/lib/widgets.dart).
> Similarly, the latter
> [reexports](https://github.com/flutter/engine/blob/master/sky/packages/sky/lib/src/widgets/basic.dart)
> many types from
> [rendering.dart](https://github.com/flutter/engine/blob/master/sky/packages/sky/lib/rendering.dart),
> such as `BoxConstraints`, that it uses in its API. On the other
> hand, it does not reexport, say, `RenderProxyBox`, since that is not
> part of the widgets API.

For the `rendering.dart` library, if you are creating new
`RenderObject` subclasses, import the entire library. If you are only
referencing specific `RenderObject` subclasses, then import the
`rendering.dart` library with a `show` keyword explicitly listing the
types you are importing. This latter approach is generally good for
documenting why exactly you are importing particularly libraries and
can be used more generally when importing large libraries for very
narrow purposes.

By convention, `dart:ui` is imported using `import 'dart:ui' show
...;` for common APIs (this isn't usually necessary because a lower
level will have done it for you), and as `import 'dart:ui' as ui show
...;` for low-level APIs, in both cases listing all the identifiers
being imported. See
[basic_types.dart](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/painting/basic_types.dart)
in the `painting` package for details of which identifiers we import
which way. Other packages are usually imported undecorated unless they
have a convention of their own (e.g. `path` is imported `as path`).

As a general rule, when you have a lot of constants, wrap them in a
class. For examples of this, see
[lib/src/material/colors.dart](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/material/colors.dart)