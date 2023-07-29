---
author: Andrey Maksimov
pubDatetime: 2022-03-01T15:22:00Z
title: From Python to Dart - Day 1
postSlug: python-to-dart-day-1
featured: false
draft: false
tags:
  - dart
  - tutorial
  - python
ogImage: ""
description:
  From Python to Dart - quickstart for Python developers. Day 1, getting to know Dart
---

# Day 1, getting to know Dart

Many developers during their work develop in a certain area and do not always consider technologies that are on the periphery of their professional tasks. However, over time, the horizons begin to expand, developers of server applications begin to look towards client applications and vice versa; those who use interpreted languages ​​look towards compiled ones; others are looking at completely different areas showing interest in the field of DevOps and others. To tame the interest of some of them, we'll talk about how the [Dart](https://dart.dev) language can help Python developers and why they need it.

## About Dart

Dart is a compiled language born somewhere in Google more than 10 years ago. At some point in time, it was positioned as an alternative to JavaScript for developing client applications, later changing its direction towards a universal language for both client applications using [Flutter](https://flutter.dev/) and server applications.

Dart is an object-oriented, class-based language with a garbage collector that is inspired by C, C#, JavaScript, and many more. Dart applications can be compiled into binary code for both desktop x86 systems and mobile ARM processors; can be run in a virtual machine, thereby providing hot-reloading and, as a result, fast development; and even web apps can be implemented with Dart and Flutter.

## Install Dart

Many OS users do not need any steps to use Python, it comes pre-installed on macOS, most if not all Linux and *BSD based distributions, and only Windows users will need to download and run the installer. Things are different with Dart.

The easiest way to try Dart is to use [DartPad](https://dartpad.dev), an editor available online that allows you to write small applications.

To install Dart on a local computer, go to the site [https://dart.dev/get-dart](https://dart.dev/get-dart) and follow one of the paths according to your operating system. So, for macOS users using [Homebrew](https://brew.sh/) just use the command:
```sh
brew install dart
```

An alternative option would be to [install](https://docs.flutter.dev/get-started/install/) Flutter, Dart will be installed as a part of it.

## Dart CLI utility

After installation, the `dart` console utility will become available to you, with which you can perform many actions that help in development:

- create new projects with `dart create`
- run them in a virtual machine with `dart run`
- compile to native code ([ahead-of-time](https://dart.dev/tools/dart-compile#exe)) using `dart compile`
- format source code with `dart format`
- generate documentation with `dart doc`
- and much more.

When using Python, we are used to installing dependencies using pip or poetry packages, but can Dart offer something similar? The answer is yes, Dart comes with a `dart pub` utility that makes it easy to install, update, publish packages, and more.

## Hello Dart

Getting to know Dart would not be complete if we did not try to write the simplest application in this language. Let's correct this omission.

```dart
// Define a function
void sayHi(String name) {
   print('Hello, $name.'); // Prints a message to the console.
}

// With this function, the application starts its work.
void main() {
   var name = 'Pythonista'; // Define and initialize the variable.
   say Hi(name); // Call the function.
}
```

As you can see, a Dart program starts its execution from the `main` function. In it we declare a variable `name`, its type will be automatically deduced based on the assigned value. After that, we call the function, passing the variable into it. Unlike Python, Dart does not rely on indentation and uses curly braces to separate blocks of code, as well as semicolons to indicate the end of executable statements. In the following chapters, we will look at the language in detail in light of how it compares to Python.

## What's next?

We will learn how to create projects using the `dart` utility and consider its capabilities in detail.
