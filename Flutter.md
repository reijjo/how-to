# FLUTTER

## Install Flutter

https://docs.flutter.dev/get-started/install

## Create Project

Mac:

- On VSCode `Shift-CMD-P` -> `Flutter: New Project` -> `Application`
- Choose folder and give your application a name

### How to run project

<details>
	<summary>Open iPhone simulator</summary>
	`open -a Simulator`
</details>

- On the bottom right corner of the VSCode click on the device name <img src='https://github.com/user-attachments/assets/813e6934-dc63-4db3-86df-f280045a5b94' alt='device name' />
- Open `lib/dart.main` in VSCode and Click the 'Play' button on the right <img src='https://github.com/user-attachments/assets/7e1a71a7-ac94-4d07-9997-669b0d7308a8' alt='play button' />
- It loads a while and then you can see the app in your iPhone Simulator

### Scaffold & AppBar

Scaffold is a wrapper for layout widgets like `AppBar` and `body`

- Simple example:

```dart
import 'package:flutter/material.dart';

void main() => runApp(MaterialApp(
        home: Scaffold(
      appBar: AppBar(
        title: const Text('My First App'),
        centerTitle: true,
      ),
      body: const Center(
        child: Text('Terve ukko!'),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
        child: const Text('Click'),
      ),
    )));

```

#### Reloading the app to see the changes

- Click the `Restart` button on the menu after making changes <img src='https://github.com/user-attachments/assets/e18fa805-5b19-4885-af6e-ee33abf76326' alt='restart' />

### Colors & Fonts

Use Custom Fonts:

- Download a font
- Make folder `fonts` to your Flutter projects root folder
- Open `pubspec.yaml` file in your projects root folder and add this to the part where to fonts are:

```yaml
fonts:
  - family: IndieFlower
    fonts:
      - asset: fonts/IndieFlower-Regular.ttf
```

<!-- - Upgrade packages on the top right <img src='https://github.com/user-attachments/assets/78d68145-faea-49bc-a2f8-d0ae1ad1de2f' alt='upgrade' /> -->

- Upgrade packages -> `flutter pub get` in the root of your app
- Add Custom Font to your code:

```dart
void main() => runApp(MaterialApp(
        home: Scaffold(
      appBar: AppBar(
        title: const Text('My First App'),
        centerTitle: true,
        backgroundColor: Colors.indigo[400],
      ),
      body: Center(
        child: Text(
          'Terve ukko!',
          style: TextStyle(
              fontSize: 20,
              fontWeight: FontWeight.bold,
              letterSpacing: 2,
              color: Colors.grey[600],
              fontFamily: 'IndieFlower'),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
        backgroundColor: Colors.indigo[400],
        child: const Text('Click'),
      ),
    )));
```

### Hot Reload

- Make a `StatelessWidget` class under your main function and move your code to there

```dart
void main() => runApp(const MaterialApp(home: Home()));

class Home extends StatelessWidget {
  // Creates a Home widget that can be used as a constant, passing the key to its parent class.
  const Home({super.key});

  @override // Overrides the build defined in classes ancestor (StatelessWidget)
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('My First App'),
        centerTitle: true,
        backgroundColor: Colors.indigo[400],
      ),
      body: Center(
        child: Text(
          'Terve ukko!',
          style: TextStyle(
            fontSize: 20,
            fontWeight: FontWeight.bold,
            letterSpacing: 2,
            color: Colors.grey[600],
            fontFamily: 'IndieFlower',
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
        backgroundColor: Colors.indigo[400],
        child: const Text('Click'),
      ),
    );
  }
}
```

- Change VSCode dart settings -> Open Settings -> Search `flutter` -> `Dart: Flutter Hot Reload On Save` -> `all`

### Images & Assets

- Image from the web

```dart
child: Image(
	image: NetworkImage('IMAGEURL')
)
```

- Asset image: Make folder `assets` in the root of your app and add image to that folder
- Open `pubspec.yaml` file in your projects root folder and add this to the part where to images are:

```yaml
assets:
  - assets/
```

- Upgrade packages -> `flutter pub get` in the root of your app
- Add image to your app

```dart
child: Image(
  image: AssetImage('assets/IMAGENAME')
)
```

- Shortcuts:

```dart
child: Image.asset('assets/IMAGENAME')
child: Image.network('IMAGEURL')
```

### Buttons & Icons

- Icon

```dart
child: Icon(
  Icons.airport_shuttle,
  color: Colors.lightBlue,
  size: 50,
 )
```

- Button

```dart
child: ElevatedButton(
  onPressed: () {
    print('you clicked me');
  },
  style: ElevatedButton.styleFrom(
    backgroundColor: Colors.lightBlue, // Set the background color
  ),
  child: const Text('click'),
)
```

The `print('you clicked me')` shows in the `Debug Console` IMAGE HERE

- Button With Icon

```dart
child: ElevatedButton.icon(
  onPressed: () {
    print('you clicked me');
  },
  icon: const Icon(Icons.mail),
  label: const Text('mail me'),
  style: ElevatedButton.styleFrom(backgroundColor: Colors.amber),
)
```

- Icon Button

```dart
child: IconButton(
  onPressed: () {
    print('You clicked me!');
  },
  icon: const Icon(Icons.alternate_email),
  color: Colors.amber,
)
```

- Code so far

```dart
import 'package:flutter/material.dart';

void main() => runApp(MaterialApp(
    theme: ThemeData(
      appBarTheme: const AppBarTheme(
        backgroundColor: Colors.indigo,
        titleTextStyle: TextStyle(color: Colors.white, fontSize: 20),
        iconTheme: IconThemeData(color: Colors.white),
      ),
      floatingActionButtonTheme: const FloatingActionButtonThemeData(
        foregroundColor: Colors.white,
      ),
    ),
    home: const Home()));

class Home extends StatelessWidget {
  // Creates a Home widget that can be used as a constant, passing the key to its parent class.
  const Home({super.key});

  @override // Overrides the build defined in classes ancestor (StatelessWidget)
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('My First App'),
        centerTitle: true,
        backgroundColor: Colors.indigo[400],
      ),
      body: Center(
          child: IconButton(
        onPressed: () {
          print('You clicked me!');
        },
        icon: const Icon(Icons.alternate_email),
        color: Colors.amber,
      )),
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
        backgroundColor: Colors.indigo[400],
        child: const Text('Click'),
      ),
    );
  }
}
```

### Containers & Padding

- Container example:

```dart
body: Container(
  padding: EdgeInsets.fromLTRB(10, 20, 30, 40),
  margin: EdgeInsets.all(30),
  color: Colors.grey[300],
  child: Text('hello'),
),
```

- Padding example:

```dart
body: const Padding(
  padding: EdgeInsets.all(30),
  child: Text('Hello, World!'),
),
```

### Rows

- Row is basically the same as flexbox/grid in CSS
- `Main Axis` is horizontal and `Cross Axis` is vertical

```dart
body: Row(
  mainAxisAlignment: MainAxisAlignment.spaceEvenly,
  crossAxisAlignment: CrossAxisAlignment.start,
  children: <Widget>[
    const Text('hello, world'),
    TextButton(
      onPressed: () {},
      style: TextButton.styleFrom(backgroundColor: Colors.amber),
      child: const Text('Click me'),
    ),
    Container(
      color: Colors.cyan,
      padding: const EdgeInsets.all(30),
      child: const Text('Inside container'),
    )
  ],
),
```

### Columns

```dart
body: Column(
  mainAxisAlignment: MainAxisAlignment.end,
  crossAxisAlignment: CrossAxisAlignment.end,
  children: <Widget>[
    const Row(
      children: [
        Text('Hello'),
        Text(' World'),
      ],
    ),
    Container(
      padding: const EdgeInsets.all(20),
      color: Colors.cyan,
      child: const Text('One'),
    ),
    Container(
      padding: const EdgeInsets.all(30),
      color: Colors.pinkAccent,
      child: const Text('Two'),
    ),
    Container(
      padding: const EdgeInsets.all(40),
      color: Colors.amber,
      child: const Text('Three'),
    ),
  ],
),
```

### Expanded Widgets

- Almost like a flexbox in CSS

```dart
body: Row(
  children: <Widget>[
    Expanded(
        flex: 3, // Takes 3/6 of the available space
        child: Image.asset('assets/ink.jpg')),
    Expanded(
      flex: 1, // Takes 1/6 of the available space
      child: Container(
        padding: const EdgeInsets.all(30),
        color: Colors.cyan,
        child: const Text('1'),
      ),
    ),
    Expanded(
      flex: 1, // Takes 1/6 of the available space
      child: Container(
        padding: const EdgeInsets.all(30),
        color: Colors.pinkAccent[100],
        child: const Text('2'),
      ),
    ),
    Expanded(
      flex: 1, // Takes 1/6 of the available space
      child: Container(
        padding: const EdgeInsets.all(30),
        color: Colors.amber,
        child: const Text('3'),
      ),
    ),
  ],
),
```

## Ninja ID Project

### Stateful Widgets

- You can change state of the variables (like useState in React)
- Change `Stateless Widget` to `Stateful Widget` by hovering on the `Stateless Widget` text and look for the bulb icon to appear on the left and just click it
- Code:

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MaterialApp(
    home: NinjaCard(),
  ));
}

class NinjaCard extends StatefulWidget {
  const NinjaCard({super.key});

  @override
  State<NinjaCard> createState() => _NinjaCardState();
}

class _NinjaCardState extends State<NinjaCard> {
  int ninjalevel = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[900],
      appBar: AppBar(
        title: const Text('Ninja ID Card'),
        centerTitle: true,
        backgroundColor: Colors.grey[850],
        titleTextStyle: const TextStyle(
            color: Colors.white, fontSize: 20, fontWeight: FontWeight.bold),
        elevation: 0,
      ),
      floatingActionButton: FloatingActionButton(
        // Floating action button that handles the ninjalevel variable
        onPressed: () {
          setState(() {
            ninjalevel += 1;
          });
        },
        backgroundColor: Colors.grey[800],
        foregroundColor: Colors.white,
        child: const Icon(Icons.add),
      ),
      body: Padding(
        padding: const EdgeInsets.fromLTRB(30, 40, 30, 0),
        child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: <Widget>[
              const Center(
                child: CircleAvatar(
                  backgroundImage: AssetImage('assets/Chun-Li.webp'),
                  radius: 60,
                ),
              ),
              Divider(
                height: 90,
                color: Colors.grey[800],
              ),
              const Text(
                'NAME',
                style: TextStyle(
                  color: Colors.grey,
                  letterSpacing: 2,
                ),
              ),
              const SizedBox(height: 10),
              Text(
                'Chun-Li',
                style: TextStyle(
                    color: Colors.amberAccent[200],
                    letterSpacing: 2,
                    fontSize: 28,
                    fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 30),
              const Text(
                'CURRENT NINJA LEVEL',
                style: TextStyle(
                  color: Colors.grey,
                  letterSpacing: 2,
                ),
              ),
              const SizedBox(height: 10),
              Text(
                '$ninjalevel', // Ninja level variable
                style: TextStyle(
                    color: Colors.amberAccent[200],
                    letterSpacing: 2,
                    fontSize: 28,
                    fontWeight: FontWeight.bold),
              ),
              const SizedBox(height: 30),
              Row(children: <Widget>[
                Icon(
                  Icons.email,
                  color: Colors.grey[400],
                ),
                const SizedBox(width: 10),
                Text(
                  'chun.li@theninja.net',
                  style: TextStyle(
                    color: Colors.grey[400],
                    fontSize: 18,
                    letterSpacing: 1,
                  ),
                ),
              ])
            ]),
      ),
    );
  }
}

```

## Quotes project

### Lists of Data

- Create new project `quotes`

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MaterialApp(
    home: QuoteList(),
  ));
}

class QuoteList extends StatefulWidget {
  const QuoteList({super.key});

  @override
  _QuoteListState createState() => _QuoteListState();
}

class _QuoteListState extends State<QuoteList> {
  List<String> quotes = [
    'Be yourself; everyone else is already taken',
    'I have nothing to declare except my genius',
    'The truth is rarely pure and never simple'
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          title: const Text('Awesome Quotes'),
          centerTitle: true,
          backgroundColor: Colors.redAccent,
          foregroundColor: Colors.white,
        ),
        body: Column(
          children: quotes.map((quote) => Text(quote)).toList(),
        ));
  }
}

```

### Custom Classes

- Make a new file `quote.dart` in the `lib` folder:

```dart
class Quote {
  String text;
  String author;

  Quote({required this.text, required this.author});
  // Quote({this.text = '', this.author = ''});
}
```

- Import the file in the `main.dart`

```dart
import 'quote.dart';
```

- And use the class in the `main.dart` file:

```dart
import 'package:flutter/material.dart';
import 'quote.dart';

void main() {
  runApp(const MaterialApp(
    home: QuoteList(),
  ));
}

class QuoteList extends StatefulWidget {
  const QuoteList({super.key});

  @override
  QuoteListState createState() => QuoteListState();
}

class QuoteListState extends State<QuoteList> {
  List<Quote> quotes = [
    Quote(
        text: 'Be yourself; everyone else is already taken',
        author: 'Oscar Wilde'),
    Quote(
        text: 'I have nothing to declare except my genius',
        author: 'Oscar Wilde'),
    Quote(
        text: 'The truth is rarely pure and never simple',
        author: 'Oscar Wilde')
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          title: const Text('Awesome Quotes'),
          centerTitle: true,
          backgroundColor: Colors.redAccent,
          foregroundColor: Colors.white,
        ),
        body: Column(
          children: quotes
              .map((quote) => Text('${quote.text} - ${quote.author}'))
              .toList(),
        ));
  }
}
```

### Cards

- Create a function under the List of quotes:

```dart
  Widget quoteTemplate(quote) {
    return Card(
      margin: const EdgeInsets.fromLTRB(16.0, 16.0, 16.0, 0),
      child: Padding(
        padding: const EdgeInsets.all(12.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: <Widget>[
            Text(
              quote.text,
              style: TextStyle(fontSize: 18.0, color: Colors.grey[600]),
            ),
            const SizedBox(height: 6.0),
            Text(
              quote.author,
              style: TextStyle(fontSize: 14.0, color: Colors.grey[800]),
            ),
          ],
        ),
      ),
    );
  }
```

- And use it in the code:

```dart
 @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          title: const Text('Awesome Quotes'),
          centerTitle: true,
          backgroundColor: Colors.redAccent,
          foregroundColor: Colors.white,
        ),
        body: Column(
          children: quotes.map((quote) => quoteTemplate(quote)).toList(),
        ));
  }
```

### Extracting Widgets

- Click on the `return Card` line inside the `quoteTemplate` Widget -> Click on the lightbulb on the left -> `Extract Widget`
- Give name to the Widget `QuoteCard` -> It makes a new `QuoteCard` Widget
- Add add that to it's own file -> `quote_card.dart`:

```dart
import 'package:flutter/material.dart';
import 'quote.dart';
import 'quote_card.dart';

void main() {
  runApp(const MaterialApp(
    home: QuoteList(),
  ));
}

class QuoteList extends StatefulWidget {
  const QuoteList({super.key});

  @override
  QuoteListState createState() => QuoteListState();
}

class QuoteListState extends State<QuoteList> {
  List<Quote> quotes = [
    Quote(
        text: 'Be yourself; everyone else is already taken',
        author: 'Oscar Wilde'),
    Quote(
        text: 'I have nothing to declare except my genius',
        author: 'Oscar Wilde'),
    Quote(
        text: 'The truth is rarely pure and never simple',
        author: 'Oscar Wilde')
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          title: const Text('Awesome Quotes'),
          centerTitle: true,
          backgroundColor: Colors.redAccent,
          foregroundColor: Colors.white,
        ),
        body: Column(
          children: quotes.map((quote) => QuoteCard(quote: quote)).toList(),
        ));
  }
}
```

- `quote.dart`:

```dart
class Quote {
  String text;
  String author;

  Quote({required this.text, required this.author});
  // Quote({this.text = '', this.author = ''});
}
```

- `main.dart`:

```dart
import 'package:flutter/material.dart';
import 'quote.dart';
import 'quote_card.dart';

void main() {
  runApp(const MaterialApp(
    home: QuoteList(),
  ));
}

class QuoteList extends StatefulWidget {
  const QuoteList({super.key});

  @override
  QuoteListState createState() => QuoteListState();
}

class QuoteListState extends State<QuoteList> {
  List<Quote> quotes = [
    Quote(
        text: 'Be yourself; everyone else is already taken',
        author: 'Oscar Wilde'),
    Quote(
        text: 'I have nothing to declare except my genius',
        author: 'Oscar Wilde'),
    Quote(
        text: 'The truth is rarely pure and never simple',
        author: 'Oscar Wilde')
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          title: const Text('Awesome Quotes'),
          centerTitle: true,
          backgroundColor: Colors.redAccent,
          foregroundColor: Colors.white,
        ),
        body: Column(
          children: quotes.map((quote) => QuoteCard(quote: quote)).toList(),
        ));
  }
}
```

### Functions as Parameters

- `main.dart`

```dart
@override
Widget build(BuildContext context) {
  return Scaffold(
      backgroundColor: Colors.grey[200],
      appBar: AppBar(
        title: const Text('Awesome Quotes'),
        centerTitle: true,
        backgroundColor: Colors.redAccent,
        foregroundColor: Colors.white,
      ),
      body: Column(
        children: quotes
            .map((quote) => QuoteCard(
                quote: quote,
                delete: () {
                  setState(() {
                    quotes.remove(quote);
                  });
                }))
            .toList(),
      ));
}
```

- `quote_card.dart`

```dart
import 'package:flutter/material.dart';
import 'quote.dart';

class QuoteCard extends StatelessWidget {
  final Quote quote;
  final VoidCallback delete;
  const QuoteCard({super.key, required this.quote, required this.delete});

  @override
  Widget build(BuildContext context) {
    return Card(
      margin: const EdgeInsets.fromLTRB(16.0, 16.0, 16.0, 0),
      child: Padding(
        padding: const EdgeInsets.all(12.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.stretch,
          children: <Widget>[
            Text(
              quote.text,
              style: TextStyle(fontSize: 18.0, color: Colors.grey[600]),
            ),
            const SizedBox(height: 6.0),
            Text(
              quote.author,
              style: TextStyle(fontSize: 14.0, color: Colors.grey[800]),
            ),
            const SizedBox(
              height: 8,
            ),
            TextButton.icon(
              onPressed: delete,
              label: const Text('delete quote'),
              icon: const Icon(Icons.delete),
            )
          ],
        ),
      ),
    );
  }
}
```

## World Time project

- Create new project `Shift-CMD-P` -> `Flutter: New Project` -> `Application` -> Choose folder -> Project name `world_time`
- Delete `test` folder
- Make a new folder inside the `lib` folder named `pages` -> Create `home.dart`, `loading.dart` and `choose_location.dart` files inside that folder
- `main.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:world_time/pages/home.dart';

void main() {
  runApp(const MaterialApp(home: Home()));
}

```

- `home.dart`:

```dart
import 'package:flutter/material.dart';

class Home extends StatefulWidget {
  const Home({super.key});

  @override
  HomeState createState() => HomeState();
}

class HomeState extends State<Home> {
  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      body: SafeArea(child: Text('home screen')),
    );
  }
}
```

- `loading.dart`:

```dart
import 'package:flutter/material.dart';

class Loading extends StatefulWidget {
  const Loading({super.key});

  @override
  State<Loading> createState() => _LoadingState();
}

class _LoadingState extends State<Loading> {
  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      body: Text('loading screen'),
    );
  }
}
```

- `choose_location.dart`:

```dart
import 'package:flutter/material.dart';

class ChooseLocation extends StatefulWidget {
  const ChooseLocation({super.key});

  @override
  State<ChooseLocation> createState() => _ChooseLocationState();
}

class _ChooseLocationState extends State<ChooseLocation> {
  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      body: Text('Choose location'),
    );
  }
}
```

### Maps & Routing

- Update `main.dart` file to have the routing:

```dart
import 'package:flutter/material.dart';
import 'package:world_time/pages/home.dart';
import 'package:world_time/pages/loading.dart';
import 'package:world_time/pages/choose_location.dart';

void main() => runApp(MaterialApp(initialRoute: '/home', routes: {
      '/': (context) => const Loading(),
      '/home': (context) => const Home(),
      '/location': (context) => const ChooseLocation(),
    }));

```

- And other files too
- `home.dart`

```dart
import 'package:flutter/material.dart';

class Home extends StatefulWidget {
  const Home({super.key});

  @override
  HomeState createState() => HomeState();
}

class HomeState extends State<Home> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: SafeArea(
          child: Column(children: <Widget>[
        TextButton.icon(
            onPressed: () {
              Navigator.pushNamed(context, '/location');
            },
            icon: const Icon(Icons.edit_location),
            label: const Text('Edit Location'))
      ])),
    );
  }
}

```

- `choose_location.dart`:

```dart
import 'package:flutter/material.dart';

class ChooseLocation extends StatefulWidget {
  const ChooseLocation({super.key});

  @override
  State<ChooseLocation> createState() => _ChooseLocationState();
}

class _ChooseLocationState extends State<ChooseLocation> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[200],
      appBar: AppBar(
        backgroundColor: Colors.blue[700],
        title: const Text('Choose a Location'),
        titleTextStyle: const TextStyle(color: Colors.white, fontSize: 20),
        iconTheme: const IconThemeData(color: Colors.white),
        centerTitle: true,
        elevation: 0,
      ),
      body: const Text('Choose location screen'),
    );
  }
}

```

### Widget Lifecycle

#### Stateless Widget

- Can't have state that changes over time
- Data doesn't change in Stateless Widget
- Build function only runs once

#### Stateful Widget

- State can change over time
- setState() triggers the build function
- `choose_location.dart`:

```dart
import 'package:flutter/material.dart';

class ChooseLocation extends StatefulWidget {
  const ChooseLocation({super.key});

  @override
  State<ChooseLocation> createState() => _ChooseLocationState();
}

class _ChooseLocationState extends State<ChooseLocation> {
  int counter = 0;
  // Runs first when the state object is created
  @override
  void initState() {
    super.initState();
    print('initState function ran');
  }

  @override
  Widget build(BuildContext context) {
    print('build function ran');

    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          backgroundColor: Colors.blue[700],
          title: const Text('Choose a Location'),
          titleTextStyle: const TextStyle(color: Colors.white, fontSize: 20),
          iconTheme: const IconThemeData(color: Colors.white),
          centerTitle: true,
          elevation: 0,
        ),
        body: ElevatedButton(
            onPressed: () {
              setState(() {
                counter += 1;
              });
            },
            child: Text('Counter is $counter')));
  }
}

```

### Asynchronous Code

- Quite similar than in JavaScript
- `Future` is like a `Promise` in JS
- `async/await` is basically the same as in JS
  `choose_location.dart`:

```dart
import 'package:flutter/material.dart';

class ChooseLocation extends StatefulWidget {
  const ChooseLocation({super.key});

  @override
  State<ChooseLocation> createState() => _ChooseLocationState();
}

class _ChooseLocationState extends State<ChooseLocation> {
  int counter = 0;

  void getData() async {
    // Simulate network request for a username
    String username = await Future.delayed(const Duration(seconds: 3), () {
      return 'Yoshi';
    });

    // Simulate network request to get bio of the username
    String bio = await Future.delayed(const Duration(seconds: 2), () {
      return 'Vegan, Musician & Egg collector';
    });

    print('$username - $bio');
  }

  // Runs first when the state object is created
  @override
  void initState() {
    super.initState();
    getData();
    print('Hey there!');
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          backgroundColor: Colors.blue[700],
          title: const Text('Choose a Location'),
          titleTextStyle: const TextStyle(color: Colors.white, fontSize: 20),
          iconTheme: const IconThemeData(color: Colors.white),
          centerTitle: true,
          elevation: 0,
        ),
        body: ElevatedButton(
            onPressed: () {
              setState(() {
                counter += 1;
              });
            },
            child: Text('Counter is $counter')));
  }
}

```

### Flutter packages (http)

- Like `npm` packages in React
- Go to `pub.dev/flutter` and search for `http`
- Install the package with the root of your apps folder with: `dart pub add http` or `flutter pub add http`
- It adds the package to your projects dependecies in your `pubspec.yaml` file:

```yaml
dependencies:
  flutter:
    sdk: flutter

  # The following adds the Cupertino Icons font to your application.
  # Use with the CupertinoIcons class for iOS style icons.
  cupertino_icons: ^1.0.8
  http: ^1.2.2
```

`main.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:world_time/pages/home.dart';
import 'package:world_time/pages/loading.dart';
import 'package:world_time/pages/choose_location.dart';

void main() => runApp(MaterialApp(initialRoute: '/', routes: {
      '/': (context) => const Loading(),
      '/home': (context) => const Home(),
      '/location': (context) => const ChooseLocation(),
    }));

```

`loading.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:http/http.dart';
import 'dart:convert';

class Loading extends StatefulWidget {
  const Loading({super.key});

  @override
  State<Loading> createState() => _LoadingState();
}

class _LoadingState extends State<Loading> {
  void getData() async {
    Response response =
        await get(Uri.parse('https://jsonplaceholder.typicode.com/todos/1'));
    Map data = jsonDecode(response.body);
    print(data);
    print(data['title']);
  }

  // Runs first when the state object is created
  @override
  void initState() {
    super.initState();
    getData();
  }

  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      body: Text('loading screen'),
    );
  }
}

```

`choose_location.dart`:

```dart
import 'package:flutter/material.dart';

class ChooseLocation extends StatefulWidget {
  const ChooseLocation({super.key});

  @override
  State<ChooseLocation> createState() => _ChooseLocationState();
}

class _ChooseLocationState extends State<ChooseLocation> {
  int counter = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          backgroundColor: Colors.blue[700],
          title: const Text('Choose a Location'),
          titleTextStyle: const TextStyle(color: Colors.white, fontSize: 20),
          iconTheme: const IconThemeData(color: Colors.white),
          centerTitle: true,
          elevation: 0,
        ),
        body: ElevatedButton(
            onPressed: () {
              setState(() {
                counter += 1;
              });
            },
            child: Text('Counter is $counter')));
  }
}

```

### .env FILE

- Install `flutter_dotenv` package `flutter pub add flutter_dotenv`
- Create `.env` file to the root of the project
- Add env file to the assets section of `pubspec.yaml`:

```yaml
assets:
  - .env
```

- Load the variables from the `.env` file in your `main.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_dotenv/flutter_dotenv.dart';
import 'package:world_time/pages/home.dart';
import 'package:world_time/pages/loading.dart';
import 'package:world_time/pages/choose_location.dart';

Future<void> main() async {
  await dotenv.load(fileName: ".env");

  runApp(MaterialApp(
    initialRoute: '/',
    routes: {
      '/': (context) => const Loading(),
      '/home': (context) => const Home(),
      '/location': (context) => const ChooseLocation(),
    },
  ));
}

```

- Import `flutter_dotenv` package into the files where you need it: `import 'package:flutter_dotenv/flutter_dotenv.dart';` and get the variable from `.env` for example like this:

```dart
String apiKey = dotenv.env['ABSTRACTAPI_APIKEY'] ?? 'ApiKey not found';
```

### WorldTime / AbstractAPi

- Get the time in the `loading.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_dotenv/flutter_dotenv.dart';
import 'package:http/http.dart';
import 'dart:convert';

class Loading extends StatefulWidget {
  const Loading({super.key});

  @override
  State<Loading> createState() => _LoadingState();
}

class _LoadingState extends State<Loading> {
  void getTime() async {
    String apiKey = dotenv.env['ABSTRACTAPI_APIKEY'] ?? 'ApiKey not found';

    // Make the request
    Response response = await get(Uri.parse(
        'https://timezone.abstractapi.com/v1/current_time/?api_key=$apiKey&location=Paris'));
    Map data = jsonDecode(response.body);
    // Get properties from data
    String datetime = data['datetime'];
    int offset = data['gmt_offset'];

    // Create DateTime object
    DateTime now = DateTime.parse(datetime);
    now = now.add(Duration(hours: offset));
    print(now);
  }

  // Runs first when the state object is created
  @override
  void initState() {
    super.initState();
    getTime();
  }

  @override
  Widget build(BuildContext context) {
    return const Scaffold(
      body: Text('loading screen'),
    );
  }
}

```

### Custom class

- Create new file `world_time.dart` for the getTime:

```dart
import 'package:http/http.dart';
import 'dart:convert';
import 'package:flutter_dotenv/flutter_dotenv.dart';

class WorldTime {
  String location; // Location name for the UI
  late String time; // Time in that location
  String flag; // Url to an asset flag icon
  String url; // Location url for api endpoint

  WorldTime({required this.location, required this.flag, required this.url});

  Future<void> getTime() async {
    String apiKey = dotenv.env['ABSTRACTAPI_APIKEY'] ?? 'ApiKey not found';

    // Make the request
    Response response = await get(Uri.parse(
        'https://timezone.abstractapi.com/v1/current_time/?api_key=$apiKey&location=$url'));
    Map data = jsonDecode(response.body);
    // Get properties from data
    String datetime = data['datetime'];
    int offset = data['gmt_offset'];

    // Create DateTime object
    DateTime now = DateTime.parse(datetime);
    now = now.add(Duration(hours: offset));

    // Set the time property
    time = now.toString();
  }
}

```

- And update the `loading.dart` file:

```dart
import 'package:flutter/material.dart';
import 'package:world_time/services/world_time.dart';

class Loading extends StatefulWidget {
  const Loading({super.key});

  @override
  State<Loading> createState() => _LoadingState();
}

class _LoadingState extends State<Loading> {
  String time = 'loading...';

  void setupWorldTime() async {
    WorldTime instance =
        WorldTime(location: 'Berlin', flag: 'germany.png', url: 'Tokyo');
    await instance.getTime();

    print(instance.time);

    setState(() {
      time = instance.time;
    });
  }

  // Runs first when the state object is created
  @override
  void initState() {
    super.initState();
    setupWorldTime();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        body: Padding(
      padding: const EdgeInsets.all(50),
      child: Text(time),
    ));
  }
}

```

### Error Handling

- Try/Catch method.
- `world_time.dart`:

```dart
import 'package:http/http.dart';
import 'dart:convert';
import 'package:flutter_dotenv/flutter_dotenv.dart';

class WorldTime {
  String location; // Location name for the UI
  late String time; // Time in that location
  String flag; // Url to an asset flag icon
  String url; // Location url for api endpoint

  WorldTime({required this.location, required this.flag, required this.url});

  Future<void> getTime() async {
    String apiKey = dotenv.env['ABSTRACTAPI_APIKEY'] ?? 'ApiKey not found';

    try {
      // Make the request
      Response response = await get(Uri.parse(
          'https://timezone.abstractapi.com/v1/current_time/?api_key=$apiKey&location=$url'));
      Map data = jsonDecode(response.body);
      // Get properties from data
      String datetime = data['datetime'];
      int offset = data['gmt_offset'];

      // Create DateTime object
      DateTime now = DateTime.parse(datetime);
      now = now.add(Duration(hours: offset));

      // Set the time property
      time = now.toString();
    } catch (error) {
      print('Caught error: $error');
      time = 'Could not get time data.';
    }
  }
}

```

### Passing Route Data

- Modify `loading.dart` and `home.dart` files:

```dart
import 'package:flutter/material.dart';
import 'package:world_time/services/world_time.dart';

class Loading extends StatefulWidget {
  const Loading({super.key});

  @override
  State<Loading> createState() => _LoadingState();
}

class _LoadingState extends State<Loading> {
  void setupWorldTime() async {
    WorldTime instance =
        WorldTime(location: 'Berlin', flag: 'germany.png', url: 'Berlin');
    await instance.getTime();

    Navigator.pushReplacementNamed(context, '/home', arguments: {
      'location': instance.location,
      'flag': instance.flag,
      'time': instance.time
    });
  }

  // Runs first when the state object is created
  @override
  void initState() {
    super.initState();
    setupWorldTime();
  }

  @override
  Widget build(BuildContext context) {
    return const Scaffold(
        body: Padding(
      padding: EdgeInsets.all(50),
      child: Text('loading...'),
    ));
  }
}

```

```dart
import 'package:flutter/material.dart';

class Home extends StatefulWidget {
  const Home({super.key});

  @override
  HomeState createState() => HomeState();
}

class HomeState extends State<Home> {
  Map data = {};

  @override
  Widget build(BuildContext context) {
    data = ModalRoute.of(context)?.settings.arguments as Map<dynamic, dynamic>;
    print('HALOO');
    print(data);

    return Scaffold(
      body: SafeArea(
          child: Column(children: <Widget>[
        TextButton.icon(
            onPressed: () {
              Navigator.pushNamed(context, '/location');
            },
            icon: const Icon(Icons.edit_location),
            label: const Text('Edit Location'))
      ])),
    );
  }
}

```

### Formatting and showing dates

- Install `intl` package (https://pub.dev/packages/intl/install)
- Format the time in `world_time.dart`:

```dart
import 'package:intl/intl.dart';

...

// Create DateTime object
DateTime now = DateTime.parse(datetime);
now = now.add(Duration(hours: offset));
// Set the time property
time = DateFormat.jm().format(now);
```

- Updated `home.dart`:

```dart
import 'package:flutter/material.dart';

class Home extends StatefulWidget {
  const Home({super.key});

  @override
  HomeState createState() => HomeState();
}

class HomeState extends State<Home> {
  Map data = {};

  @override
  Widget build(BuildContext context) {
    data = ModalRoute.of(context)?.settings.arguments as Map<dynamic, dynamic>;
    print('HALOO');
    print(data);

    return Scaffold(
      body: Padding(
        padding: const EdgeInsets.fromLTRB(0, 120, 0, 0),
        child: SafeArea(
            child: Column(children: <Widget>[
          TextButton.icon(
              onPressed: () {
                Navigator.pushNamed(context, '/location');
              },
              icon: const Icon(Icons.edit_location),
              label: const Text('Edit Location')),
          const SizedBox(height: 20),
          Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              Text(data['location'],
                  style: const TextStyle(letterSpacing: 2, fontSize: 28))
            ],
          ),
          const SizedBox(height: 20),
          Text(
            data['time'],
            style: const TextStyle(fontSize: 66),
          )
        ])),
      ),
    );
  }
}

```

### Spinners

- Install `flutter_spinkit`: `flutter pub add flutter_spinkit`
- Update the `loading.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:world_time/services/world_time.dart';
import 'package:flutter_spinkit/flutter_spinkit.dart';

class Loading extends StatefulWidget {
  const Loading({super.key});

  @override
  State<Loading> createState() => _LoadingState();
}

class _LoadingState extends State<Loading> {
  void setupWorldTime() async {
    WorldTime instance =
        WorldTime(location: 'Berlin', flag: 'germany.png', url: 'Berlin');
    await instance.getTime();

    Navigator.pushReplacementNamed(context, '/home', arguments: {
      'location': instance.location,
      'flag': instance.flag,
      'time': instance.time
    });
  }

  // Runs first when the state object is created
  @override
  void initState() {
    super.initState();
    setupWorldTime();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.blue[900],
        body: const Center(
            child: SpinKitSpinningLines(
          color: Colors.white,
          size: 80,
        )));
  }
}

```

### Ternary Operators

- Add boolean to `world_time.dart` variables:

```dart
late bool isDaytime; // True or false if daytime or not
```

- And set a ternary inside the try/catch block:

```dart
// Set the time property & isDayTime
isDaytime = now.hour > 6 && now.hour < 20 ? true : false;
time = DateFormat.jm().format(now);
```

- Create `assets` folder in the root of your project and add there 2 images (day and night (https://github.com/iamshaunjp/flutter-beginners-tutorial/tree/lesson-33/world_time_app/assets)) -> Add to `pubspec.yaml` file in the assets:

```yaml
assets:
  - .env
  - assets/
```

- Update the packages `flutter pub get`
- `choose_location.dart`:

```dart
import 'package:flutter/material.dart';

class ChooseLocation extends StatefulWidget {
  const ChooseLocation({super.key});

  @override
  State<ChooseLocation> createState() => _ChooseLocationState();
}

class _ChooseLocationState extends State<ChooseLocation> {
  int counter = 0;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.grey[200],
        appBar: AppBar(
          backgroundColor: Colors.blue[700],
          title: const Text('Choose a Location'),
          titleTextStyle: const TextStyle(color: Colors.white, fontSize: 20),
          iconTheme: const IconThemeData(color: Colors.white),
          centerTitle: true,
          elevation: 0,
        ),
        body: ElevatedButton(
            onPressed: () {
              setState(() {
                counter += 1;
              });
            },
            child: Text('Counter is $counter')));
  }
}
```

- `home.dart`:

```dart
import 'package:flutter/material.dart';

class Home extends StatefulWidget {
  const Home({super.key});

  @override
  HomeState createState() => HomeState();
}

class HomeState extends State<Home> {
  Map data = {};

  @override
  Widget build(BuildContext context) {
    data = ModalRoute.of(context)?.settings.arguments as Map<dynamic, dynamic>;
    print(data);

    // Set background
    String bgImage = data['isDaytime'] ? 'day.png' : 'night.png';

    return Scaffold(
      body: Container(
        decoration: BoxDecoration(
            image: DecorationImage(
                image: AssetImage('assets/$bgImage'), fit: BoxFit.cover)),
        child: Padding(
          padding: const EdgeInsets.fromLTRB(0, 120, 0, 0),
          child: SafeArea(
              child: Column(children: <Widget>[
            TextButton.icon(
                onPressed: () {
                  Navigator.pushNamed(context, '/location');
                },
                icon: Icon(
                  Icons.edit_location,
                  color: Colors.grey[300],
                ),
                label: Text('Edit Location',
                    style: TextStyle(
                      color: Colors.grey[300],
                    ))),
            const SizedBox(height: 20),
            Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                Text(data['location'],
                    style: const TextStyle(
                        letterSpacing: 2, fontSize: 28, color: Colors.white))
              ],
            ),
            const SizedBox(height: 20),
            Text(
              data['time'],
              style: const TextStyle(fontSize: 66, color: Colors.white),
            )
          ])),
        ),
      ),
    );
  }
}

```

- `loading.dart`

```dart
import 'package:flutter/material.dart';
import 'package:world_time/services/world_time.dart';
import 'package:flutter_spinkit/flutter_spinkit.dart';

class Loading extends StatefulWidget {
  const Loading({super.key});

  @override
  State<Loading> createState() => _LoadingState();
}

class _LoadingState extends State<Loading> {
  void setupWorldTime() async {
    WorldTime instance =
        WorldTime(location: 'Berlin', flag: 'germany.png', url: 'Berlin');
    await instance.getTime();

    Navigator.pushReplacementNamed(context, '/home', arguments: {
      'location': instance.location,
      'flag': instance.flag,
      'time': instance.time,
      'isDaytime': instance.isDaytime
    });
  }

  // Runs first when the state object is created
  @override
  void initState() {
    super.initState();
    setupWorldTime();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
        backgroundColor: Colors.blue[900],
        body: const Center(
            child: SpinKitSpinningLines(
          color: Colors.white,
          size: 80,
        )));
  }
}

```

- `world_time.dart`

```dart
import 'package:http/http.dart';
import 'dart:convert';
import 'package:flutter_dotenv/flutter_dotenv.dart';
import 'package:intl/intl.dart';

class WorldTime {
  String location; // Location name for the UI
  late String time; // Time in that location
  String flag; // Url to an asset flag icon
  String url; // Location url for api endpoint
  late bool isDaytime; // True or false if daytime or not

  WorldTime({
    required this.location,
    required this.flag,
    required this.url,
  });

  Future<void> getTime() async {
    String apiKey = dotenv.env['ABSTRACTAPI_APIKEY'] ?? 'ApiKey not found';

    try {
      // Make the request
      Response response = await get(Uri.parse(
          'https://timezone.abstractapi.com/v1/current_time/?api_key=$apiKey&location=$url'));
      Map data = jsonDecode(response.body);
      // Get properties from data
      String datetime = data['datetime'];
      int offset = data['gmt_offset'];

      // Create DateTime object
      DateTime now = DateTime.parse(datetime);
      now = now.add(Duration(hours: offset));

      // Set the time property & isDayTime
      isDaytime = now.hour > 6 && now.hour < 20 ? true : false;
      time = DateFormat.jm().format(now);
    } catch (error) {
      print('Caught error: $error');
      time = 'Could not get time data.';
    }
  }
}

```

- `main.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:flutter_dotenv/flutter_dotenv.dart';
import 'package:world_time/pages/home.dart';
import 'package:world_time/pages/loading.dart';
import 'package:world_time/pages/choose_location.dart';

Future<void> main() async {
  await dotenv.load(fileName: ".env");

  runApp(MaterialApp(
    initialRoute: '/',
    routes: {
      '/': (context) => const Loading(),
      '/home': (context) => const Home(),
      '/location': (context) => const ChooseLocation(),
    },
  ));
}

```

### List View Builder

`choose_location.dart`:

```dart
import 'package:flutter/material.dart';
import 'package:world_time/services/world_time.dart';

class ChooseLocation extends StatefulWidget {
  const ChooseLocation({super.key});

  @override
  State<ChooseLocation> createState() => _ChooseLocationState();
}

class _ChooseLocationState extends State<ChooseLocation> {
  List<WorldTime> locations = [
    WorldTime(location: 'London', flag: 'uk.png', url: 'London'),
    WorldTime(location: 'Berlin', flag: 'germany.png', url: 'Berlin'),
    WorldTime(location: 'Cairo', flag: 'egypt.png', url: 'Cairo'),
    WorldTime(location: 'Nairobi', flag: 'kenya.png', url: 'Nairobi'),
    WorldTime(location: 'Chicago', flag: 'usa.png', url: 'Chicago'),
    WorldTime(location: 'New York', flag: 'usa.png', url: 'New York'),
    WorldTime(location: 'Seoul', flag: 'south_korea.png', url: 'Seoul'),
    WorldTime(location: 'Jakarta', flag: 'indonesia.png', url: 'Jakarta'),
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[200],
      appBar: AppBar(
        backgroundColor: Colors.blue[700],
        title: const Text('Choose a Location'),
        titleTextStyle: const TextStyle(color: Colors.white, fontSize: 20),
        iconTheme: const IconThemeData(color: Colors.white),
        centerTitle: true,
        elevation: 0,
      ),
      body: ListView.builder(
        itemCount: locations.length,
        itemBuilder: (context, index) {
          return Padding(
            padding: const EdgeInsets.symmetric(vertical: 1, horizontal: 4),
            child: Card(
              child: ListTile(
                onTap: () {
                  print(locations[index].location);
                },
                title: Text(locations[index].location),
                leading: CircleAvatar(
                  backgroundImage:
                      AssetImage('assets/${locations[index].flag}'),
                ),
              ),
            ),
          );
        },
      ),
    );
  }
}
```
