---
author: Andrey Maksimov
pubDatetime: 2022-11-04T15:22:00Z
title: Reading CSV files in Dart
postSlug: reading-csv-files-with-dart
featured: true
draft: false
tags:
  - dart
  - tutorial
  - csv
ogImage: ""
description: We read CSV files in Dart from synchronous mode to asynchronous Streams.
---

## What is a CSV files

CSV is a common format for transferring tabular data between different programs. Its history goes back centuries, to a time when computers were big and trees were green. In the general case, it represents data separated by commas and collected in lines so that each line defines a specific record. However, variations occur: for example, some programs separate values ​​with tabs rather than commas, while also using the `.csv` extension, which can cause applications to crash.

Let's see an example of such data:

```csv
id, title, type, count
1, "Citroen C3", Car, 2
2, "KTM 890 ADVENTURE R", Motorcycle, 4
3, "Jeep Avenger", Car, 1
4, "Polestar P1", Car, 3
```

As you can see, it looks like we have presented the table as text: each row is a file row, and the first row is a header with column names. You may also notice that double quotes sometimes surround strings, this is done in case the columns contain control characters or other characters that can break formatting, such as commas. Actually, there is an RFC describing CSV format: [RFC4180](https://datatracker.ietf.org/doc/html/rfc4180).

Today we will focus on how to read such files, how to do it efficiently, and even try to determine the data types inside the strings. So let's get started.

## Read CSV file synchronously

Let's start by defining the API we want to get. For our program, the following is enough:

- get the file name when running from the command line
- check that the name is passed, otherwise, we print out the problem to the console and exit
- if the path is specified, read the file and output all lines sequentially to the console and exit

```dart
import 'dart:io';

import 'package:csv_reading/csv_reading.dart' as csv_reading;

void main(List<String> arguments) {
  if (arguments.isEmpty) {
    print("Usage: csv_reading path/to.csv\n");
    exit(1);
  }
  final path = arguments[0];

  final lines = csv_reading.readCsvSync(path);
  lines?.forEach((element) => print(element));
}
```

Now that we've defined the interface, we need to write the `readCsvSync` method. It must take a string pointing to a file on the user's computer, check for its existence, and after reading the entire file, return all the strings as a list.

```dart
import 'dart:io';

List<String>? readCsvSync(String path) {
  // Create `File` object
  final file = File(path);
  // Check if file is ready to be read :)
  if (!file.existsSync()) {
    return null;
  }

  // Read lines sync and return them.
  return File(path).readAsLinesSync();
}
```

On this, our minimal program is ready, you can run it from the command line.

```shell
$ dart run bin/csv_reader.dart path/to/data.csv
id, title, type, count
1, "Citroen C3", Car, 2
2, "KTM 890 ADVENTURE R", Motorcycle, 4
3, "Jeep Avenger", Car, 1
4, "Polestar P1", Car, 3
```

## Async reading

Working in synchronous mode is simple and straightforward, but from a program performance point of view, it is not always the best option, since it completely blocks the program from executing until the end of reading. What can we do about it? For example, we can use asynchronous reading! I will not go into the very concept of asynchronous work and how it is implemented in the Dart language, if you are not already familiar, then this will be your [homework] (https://dart.dev/guides/language/language-tour#asynchrony- support). Let's move on to solving our problem.

So, first, we need to change the interface of our program and method calls from synchronous to asynchronous. To do this, just add the `await` keyword before calling the method. But how can we call an async method inside a synchronous `main()`? I'm afraid not, but Dart gives us the ability to make the `main()` method asynchronous!

```dart
void main(List<String> arguments) async {
  if (arguments.isEmpty) {
    print("Usage: csv_reading path/to.csv\n");
    exit(1);
  }
  final path = arguments[0];

  final lines = await csv_reading.readCsv(path);
  lines?.forEach((element) => print(element));
}
```

Now our `main()` method will look like this. As you can see, not much has changed.

Now we need to implement the `readCsv(String)` method. First, we can no longer return a list of strings, asynchronous methods must return a `Future` object, which, in turn, will contain the necessary data. Also, we must mark our method as asynchronous, as we did with the `main()` method before, by adding the keyword `async` to its description. After that, we can use asynchronous file methods such as `exists()` and `readAsLines()`.

```dart
// Wrap response in a `Future` object and mark function as `async`
Future<List<String>?> readCsv(String path) async {
  // Create `File` object
  final file = File(path);
  // Check if file is ready to be read :)
  if (!await file.exists()) {
    return null;
  }

  // Read lines sync and return them.
  return await File(path).readAsLines();
}
```

Still easy enough, right? Let's move on!

## Async reading with Streams

One of the disadvantages of our program in its current form is that it reads the entire file, after which we can work with it. Yes, despite being asynchronous, we're still loading the entire file into memory, we're just doing it asynchronously. And although this will not be a problem for small files, if our program must process files of tens and hundreds of megabytes, this can become a problem: not every computer may have enough memory.

Dart has special classes that allow us to work with a stream of data by processing it on the fly, thereby saving memory and time. One such class is [Stream](https://api.dart.dev/stable/2.18.4/dart-async/Stream-class.html). You can always read more about it in the official documentation and [articles](https://dart.dev/articles/libraries/creating-streams). For our program, 2 points are important:

- how to create a `Stream` to read a file
- how to return a `Stream` with a stream of strings

To achieve this, we need to make a few changes to our program. First, we need to change the description of our method so that Dart knows that it returns a `Stream` instead of a list of strings, to do this, we need to convert our method to a generator. This is easy to do with the `async*` keyword and changing the return type to `Stream<String>` along the way:

```dart
Stream<String> streamCsv(String path) async * {
    ...
}
```

Next, instead of reading the whole file, we need to open the stream for reading, like this: `file.openRead()`. But here's the catch: when reading this stream, it will return a stream of bytes to us, not lines; we don't want to work with bytes: it's the lines in the file that are important to us. What do we do? How to transform the stream in the way we need? For this, Dart has special classes [StreamTransformer](https://api.dart.dev/stable/2.18.4/dart-async/StreamTransformer-class.html), they will help us perform transformations, namely: get characters from a set of bytes; split one endless stream of characters into lines, detecting the end of the line by the control characters `\n` and [other](https://api.dart.dev/stable/2.18.4/dart-convert/LineSplitter-class.html).

```dart
// Don't forget to import `dart:convert` package
import 'dart:convert';
...

final stream = file.openRead()
    // Convert bytes to string
    .transform(utf8.decoder)
    // Splint string into a lines
    .transform(LineSplitter());
```

Now that we have a stream of lines from a file, all we need to do is return them to the caller, but without blocking the program, as a stream, using `await for` and `yield`:

```dart
// Iterate over the stream
await for (String row in stream) {
    // return generated value from the our generator.
    yield row;
}
```

Excellent! All that remains for us is to learn how to listen to the stream and print the received data to the console. I think you yourself already know how to achieve this.

```dart
await for (final line in csv_reading.streamCsv(path)) {
    print(line);
}
```

## Type detection

Awesome! Now we have a list of values, it remains only to determine their types! Let's proceed as follows:

1. first, trim all spaces at the beginning and end of our value
2. try to convert the value to an integer using `int.tryParse(value)`
3. if the received value is equal to `null`, then this is not a number :)
4. go ahead and try to convert to fractional `double.tryParse(value)`
5. if the received value is equal to `null`, then this is not a fractional number :)
6. check if our value is framed in `quotechar` and if so, cut them off, leaving only the value.
7. we collect values ​​in the list and we return from the generator.

The algorithm is ready, let's write the code!

```dart
    ...
    final items = row.split(delimeter);

    // Iterate over each element in the items list
    final values = items.map((element) {
        // 1 trim spaces
        element = element.trim();

        // 2. Check if the value is int
        var ival = int.tryParse(element);
        // 3. Parsed successfully?
        if (ival != null) return ival;

        // 4. Try to parse into double value
        var dval = double.tryParse(element);
        // 5. Parsed successfully?
        if (dval != null) return dval;

        // 6. Check if the string is quoted
        if (element.startsWith(quotechar) && element.endsWith(quotechar)) {
            element = element.substring(1, element.length - 1);
        }

        return element;
    });

    // 7. Return final list
    yield values.toList();
}
```

Now you can check the return types and make sure it's not just strings anymore!

## Fin

Great, the full source code for these examples can be found in the [https://github.com/amka/csv_reading_article](https://github.com/amka/csv_reading_article) repository.
