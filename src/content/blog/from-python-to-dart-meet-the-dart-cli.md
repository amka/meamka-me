---
author: Andrey Maksimov
pubDatetime: 2022-06-13T10:22:00Z
title: From Python to Dart - Day 2, Meet the dart CLI
postSlug: from-python-to-dart-meet-the-dart-cli
featured: false
draft: false
tags:
  - dart
  - tutorial
  - python
ogImage: ""
description:
  From Python to Dart - Day 2 - Meet the dart CLI.
---


The Dart language is unimaginable without the command-line utility (CLI) of the same name. This is a full-fledged tool that greatly simplifies the life of a developer in terms of its capabilities, far exceeding the built-in capabilities of the Python interpreter.

Among the `dart` features available are project creation, source code analyzing and formatting, and even publishing packages to [pub.dev](https://pub.dev) and more.

> Bear in mind that the goal of this cycle is not a complete immersion in Dart from scratch, but a quick acquaintance for those who already know how to develop, in particular, in the Python language.

## Projects creation

Python does not have built-in capabilities for automated project creation, everything has to be done manually: create a folder structure, describe dependencies in `requirements.txt`. Of course, there are third-party applications that make this process easier, such as  [poetry](https://python-poetry) or [Cookiecutter](https://cookiecutter.readthedocs.io/). However, they are third-party and require installation in a system or virtual environment. Dart has this feature built-in.

### dart create

So, `dart create` is a CLI utility that can help you to create a project from the specified template.

```
$ dart create project_name
```

By default, this is a simple console application like [dart-sass](https://sass-lang.com/dart-sass). However, this is not all. Using the `-t` option, you can specify which template to use for creation, it can be:

| `console-simple` | A simple console application (basic template). |
| ---------------- | ------------------------------------------------------ |
| `console-full` | Console application with tests. |
| `package-simple` | A starting point for Dart libraries or apps. |
| `server-shelf` | Server based on [shelf](https://pub.dev/packages/shelf). |
| `web-simple` | Web application with core libraries. |

When run, `dart create` will first create the project structure and then try to load all required libraries from [pub.dev](https://pub.dev). This behavior can be changed by specifying the `--no-pub` flag when invoking the command.

> Analogue from the world of Python: `poetry new project_name`

## Analyzing and formatting

When working with several people on one project, it is very often necessary to use a single style of code design, writing comments, using variable names, etc. In the Python world, we use linters [flake8](https://flake8.pycqa.org/), various formatters ([black](https://black.readthedocs.io/), [yapf](https://github.com/google/yapf), [autopep8](https://pypi.org/project/autopep8/)) and [mypy](http://www.mypy-lang.org/) for type checking. How can Dart help meet these challenges?

### dart analyze

The utility allows you to perform [static analysis](https://dart.dev/guides/language/analysis-options) of the source code and point out errors and shortcomings.

By default, the call will run a check and indicate errors and warnings, indicating the need to respond to the `info` level with the `--fatal-infos` flag.

```
$ dart analyze [<DIRECTORY> | <DART_FILE>]
```

The behavior of the analyzer can be adjusted using the configuration file or comments in the code. 

### dart format

Using the command, we can style our source code according to the [Dart guide](https://dart.dev/guides/language/effective-dart/style#formatting).

```shell
$ dart format
```

This command will overwrite the files, formatting them "properly".

### dart fix

```shell
$ dart fix
```

Applies automatic fixes to source code files, fixing two types of errors:

- Analysis of issues related to automatic fixes (sometimes called *quick-fixes* or *code actions*)
- Issues related to package API transfer information

To see the changes, specify the `--dry-run` flag; to apply `--apply`; there is no *default* behavior for this command.

## Building / Compilation

### dart run

Launching a Dart application is extremely simple, just run the following command in the terminal:

```bash
$ dart run bin/myapp.dart
```

If you are inside a package and in the `bin` directory there is a dart file with the same name as the package name, then the application can be done without specifying the file:

```bash
$dart run
```

It is also possible to run applications from the packages specified in the dependencies to the current. For example, if your application has a dependency on the package `foo`, to run a program in this package, just call the command:

```bash
$ dart run foo
```

Or specify a different program inside the `foo` package:

```shell
$ dart run foo:bar
```

#### Arguments and flags

Often when developing, you need to pass arguments to the `main()` function, for this you need to pass them at startup after specifying the file:

```bash
$ dart run bin/myapp.dart arg1 arg2
```

You can also specify additional flags, for example, to debug the application:

```bash
$ dart run --enable-asserts bin/myapp.dart arg1 arg2
```

### dart compile

The `dart compile` command allows you to compile a Dart program for the target platform. The output you specify with a subcommand can either include the Dart runtime or be a module (also known as a snapshot).

```shell
$ dart compile
```

| Subcommand | Conclusion | Description |
| ------------- | ---------------- | -------|
| `exe` | Executable | A standalone, architecture-specific executable containing source code compiled to native code and a small Dart runtime. |
| `aot-snapshot` | AOT module | An architecture-specific file containing source code compiled to native code, but **does not contain** the Dart runtime. |
| `jit-snapshot` | JIT Module | An architecture-specific file with an intermediate representation of the entire source code, as well as an optimized representation of the source code that was running during the training run of the program. JIT code may have higher peak performance than AOT code, but it depends on the training run. |
| `kernel` | kernel module | A portable intermediate representation of the source code. [More](https://dart.dev/tools/dart-compile#kernel) |
| `js` | JavaScript | JavaScript file with application. [More](https://dart.dev/tools/dart-compile#js) |

### dart test

Every program needs testing, this will reduce the number of errors during development and improve the quality of the application. For Python, there is an excellent framework that simplifies the work with tests, writing, and executing them - [Pytest](https://docs.pytest.org/). Running tests with it is extremely simple:

```shell
$ pytest
```

The output of the command is quite detailed and makes it easy to understand where and what went wrong when running the tests. Here's what it looks like:

![pytest example](/assets/from-python-to-dart-meet-the-dart-cli/pytest.png)

Testing in Dart is done with the `dart test` command.

```shell
$ dart test
```

Several flags such as `--name` (`-n`), `--tags` (`-t`), or `--exclude-tags` (`-x`) allow you to control which tests will be run. If several flags are specified, then tests that satisfy all conditions at once will be executed.

![dart test example](/assets/from-python-to-dart-meet-the-dart-cli/dart-test.png)

## Package management

### dart pub

Python has a built-in `pip` utility that allows you to easily manage packages, install, upgrade, and remove them. For more control, there are also third-party tools like poetry. Dart also has a tool that makes it easier to work with external dependencies - this is `dart doc`.

```shell
$ dart pub
```

The possibilities of `dart pub` are much wider than installing and removing packages. With it, you can manage the local package cache, publish packages to [pub.dev](https://pub.dev), etc.

For example, consider installing a package in Python:

```shell
$ python3 -m pip install django
```

This command will install the up-to-date version of the Django package, however, it will not write it as a dependency, which means that on a new installation, you need to learn about this dependency somehow.

The same can be done in Dart like this:

```shell
$ dart pub add shelf
```

In this case, the package [shelf](https://pub.dev/packages/shelf) will be added to the project, specified as a dependency in [pubspec.yml](https://dart.dev/tools/pub/pubspec) and loaded into the cache.

> Analogs in Python: [pip](https://pip.pypa.io/en/stable/), [poetry](https://python-poetry.org/).

## Documentation

### dart doc

An important part of the development of applications and libraries is the documentation of functionality and source code. And for this, Dart has a special utility, which is extremely easy to run:

```shell
$ dart doc
```

![Sample generated dart doc documentation](/assets/from-python-to-dart-meet-the-dart-cli/dart-doc.png)

Dart allows you to use comments and markup in your code to describe classes, functions, modules, and other structures. We'll look at these in more detail in a separate article, but for now, we'll mention that Dart handles special document comments. You can read about how to make documentation as effective as possible in the article [Effective Dart](https://dart.dev/guides/language/effective-dart/documentation).

> Analogues in Python can be the generator [Sphinx](https://www.sphinx-doc.org/) or [pdoc](https://pdoc3.github.io/pdoc/), their capabilities are far beyond the capabilities of `dart doc`.

## Conclusion

We have reviewed some of the commands available using the `dart` utility, some of them require a deeper understanding. However, even this cursory glance gives an idea of ​​its capabilities and convenience available "out of the box" for the developer.

Next, we'll start diving into the Dart language and compare it to Python.