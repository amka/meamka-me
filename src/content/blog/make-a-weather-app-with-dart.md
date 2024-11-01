---
author: Andrey Maksimov
pubDatetime: 2023-07-29T23:57:00Z
title: Make a weather app with Dart
postSlug: make-a-weather-app-with-dart
featured: true
draft: false
tags:
  - dart
  - tutorial
  - weather
  - beginner
ogImage: ""
description: How to write a simple command-line application to get current weather by using OpenMeteo API.
---

Today we're going to build a console application that can fetch and show weather for the given location. Under the hood, it uses [Open-Meteo](https://open-meteo.com/) as a weather data source. In addition to the Dart language, we will use several libraries to implement the application:

- [args](https://pub.dev/packages/args) to parse command-line arguments
- [http](https://pub.dev/packages/http) to fetch the data via API
- [console](https://pub.dev/packages/console) to make output of the app more fancy ✨

## Concept

<iframe width="736" height="315" src="https://www.youtube.com/embed/LUFLze5W7QA?si=8lG1TCJLfqAdVigl" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

To start developing, we need to design how our application should work: how to get input from the user, how to determine what coordinates the place is located and how to fetch weather conditions and forecasts for this location. While it seems like a small application we never know do we want to grow it into something bigger or not, and as good developers do, we have to think about architecture before we start. Let's split these tasks into 3 steps.

### User's input

Command-line or console applications usually work straightforwardly from start to end. Users can pass the data to process in the beginning by entering values as arguments to the application.

For example:

```bash
$ dart compile exe -o myApp bin/myApp.dart
```

That's a lot, I know, but let's see what is presented here.
`dart` - is the application itself. Next goes a bunch of arguments:

- `compile`, `exe` - command and subcommand to the `dart` program, it describes the main command to execute.
- `-o myApp` - argument, which tells what the name of the output file should be.
- `bin/myApp.dart` - the last argument pointing to the filename to be compiled.

There could be more or less arguments, all of them passed and parsed when application starts. But, how to do that?

## Parsing arguments

Dart has a package to work with arguments, it helps to design a command-line interface, parse the arguments and show usage examples if needed. It is called [args](https://pub.dev/packages/args).

To start, we need to instantiate the object of the [ArgParser](https://pub.dev/documentation/args/latest/args/ArgParser-class.html) class and then add options, arguments, and flags to let the parser know what we are expecting for input.

```dart
import 'package:args/args.dart';

void main() {
  // Initialize the ArgParser object
  final argParser = ArgParser()
    // Add a boolean flag.
    ..addFlag('debug', help: 'enable debug mode')
    ..addFlag('select',
        abbr: 's',
        help: 'select from found locations, otherwise first found will be used')
    // Then add options that can modify app behavior
    ..addOption('temp_unit',
        abbr: 't',
        defaultsTo: 'celsius',
        allowed: ['celsius', 'fahrenheit'],
        help: 'temperature units')
    ..addOption('wind_unit',
        abbr: 'w',
        defaultsTo: 'kmh',
        allowed: ['kmh', 'ms', 'mph', 'kn'],
        help: 'wind speed units')
    ..addOption('prec_unit',
        abbr: 'p',
        defaultsTo: 'mm',
        allowed: ['mm', 'inch'],
        help: 'precipitation units');
}
```

As you can see, flags are kind of indicators, they can be `true` or `false`. On the other hand, options can take any values but they can be limited to `allowed` values only. There is a much more feature available, like commands or rest values such as in the example above, and you can read about them in the [package documentation](https://pub.dev/packages/args).

## Finding the location

When user input is collected and parsed, our application logic comes first. It starts with the need to find the coordinates of a given location because the Open-Meteo API requires coordinates to provide us with a forecast. To do this, we can use reverse geocoding, and for these purposes Open-Meteo offers us the API method https://open-meteo.com/ru/docs/geocoding-api, to which we must pass the name of the place, and in response we will receive a list from suitable locations. Each location contains a lot of geographic information, but we will only be interested in the name and coordinates.

Let's start with an API request. We need a function that takes a location name and returns a list of locations from the API response. Something like this:

```dart
Future<List<Location>?> getCoordinates(String locationName,
    {int count = 5}) async {
    // Send request and parse response
}
```

In order to make a request we need to build a `Uri` object.

```dart
  final uri = Uri.https(
    'geocoding-api.open-meteo.com',
    '/v1/search',
    {'name': locationName, 'count': count.toString()},
  );
```

Now we can send an HTTP request and process results:

```dart
 try {
    // Send request and await for response.
    final result = await http.get(uri).timeout(Duration(seconds: 5));

    // Check response HTTP Code.
    if (result.statusCode != HttpStatus.ok) return null;

    // Try to parse JSON body into a list of `Location` objects
    final jsonBody = jsonDecode(result.body) as Map<String, dynamic>;
    if (jsonBody['results'] == null) return null;

    return jsonBody['results']
        .map<Location>((item) => Location.fromJson(item))
        .toList();
  } on TimeoutException {
    print('Operation timed out');
    return null;
  } catch (e) {
    print('Error occured while getting location coordinates: $e');
    return null;
  }
```

Our `Location` model will look like this:

```dart
class Location {
  final double latitude;
  final double longitude;
  final String name;
  final String? country;
  final String? countryCode;

  Location({
    required this.latitude,
    required this.longitude,
    required this.name,
    this.country,
    this.countryCode,
  });

  factory Location.fromJson(Map<String, dynamic> json) => Location(
        name: json['name'],
        latitude: json['latitude'].toDouble(),
        longitude: json['longitude'].toDouble(),
        country: json['country'],
        countryCode: json['country_code'],
      );

  @override
  String toString() {
    return '<Location: $name, $country>';
  }
}
```

Cool! Now we have a list with `Location`-s and thus we can go to the next part - get the current weather conditions!

## Current weather condition

Let's start with defining `CurrentWeather` model. As you may see in the Open-Meteo API condition can contain a lot of data such as temperature, precipation, UV-level and more. To make our code compact we will care only about few of them: temperature, weather code and wind speed.

```dart
class CurrentWeather {
  final double temperature;
  final double windSpeed;
  final int weatherCode;

  CurrentWeather(
      {required this.temperature,
      required this.windSpeed,
      required this.weatherCode});

  factory CurrentWeather.fromJson(Map<String, dynamic> json) => CurrentWeather(
        weatherCode: json["weathercode"].toInt(),
        temperature: json["temperature"].toDouble(),
        windSpeed: json["windspeed"].toDouble(),
      );
}
```

All that remains for us is to form a request to the server and process the response in the same way as we did in the getCoordinates function:

- create a Uri object
- using the http library we send a request to the API and wait for a response
- check that the response contains no errors and try to parse JSON into a CurrentWeather object.

```dart
Future<CurrentWeather?> getCurrentWeather(Location coordinates) async {
  final url = Uri.https(
    'api.open-meteo.com',
    '/v1/forecast',
    {
      'latitude': coordinates.latitude.toString(),
      'longitude': coordinates.longitude.toString(),
      'current_weather': true.toString()
    },
  );

  try {
    final result = await http.get(url).timeout(Duration(seconds: 5));
    if (result.statusCode != HttpStatus.ok) return null;

    final jsonData = json.decode(result.body)['current_weather'];
    return CurrentWeather.fromJson(jsonData);
  } on TimeoutException {
    print('Operation timed out');
    return null;
  } catch (e) {
    print('Error occured while getting location coordinates: $e');
    return null;
  }
}
```

## Finishing steps

In order to make the users of our application a little more satisfied, I added a function for a beautiful output of the received data.

```dart
void printWeather(Location location, CurrentWeather weather) {
  final temp = weather.temperature > 0
      ? format('{color.gold}${weather.temperature}{color.end}')
      : format('{color.blue}${weather.temperature}{color.end}');

  var condition = '🌚';
  switch (weather.weatherCode) {
    case 0:
      // 	Clear sky
      condition = '☀️';
      break;
    case 1:
      // Mainly clear
      condition = '🌤';
      break;
    case 2:
      // Partly cloudy
      condition = '⛅️';
      break;
    case 3:
      // 	Overcast
      condition = '☁️';
      break;
    default:
      break;
  }
  print('${location.name}, ${location.country} $condition  $temp°C');
}
```

Thank you for being with me, like, share, and subscribe are always appreciated :heart: ! You can find full code of this article on GitHub https://github.com/amka/spotty. See you in the next chapters!
