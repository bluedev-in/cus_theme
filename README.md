# Custom Theme Package

A Flutter package that provides a customizable theme system for your applications.  
Easily implement and switch between light and dark themes, customize colors, typography, spacing, and more through a clean, consistent API.

---

## Features

- Pre-configured light and dark themes
- Custom color schemes and theme presets
- Consistent text styles and typography system
- Spacing, sizing, and animation constants
- Dynamic theme switching (light/dark/custom)
- Theme provider with persistence (using `shared_preferences`)
- Custom theme extensions for extra properties
- Responsive design utilities
- Type-safe theme access and context extensions

---

## Getting started

## Installation

Run this command in your project directory:

```sh
flutter pub add cus_theme
```

Or manually add it to your `pubspec.yaml`:

```yaml
dependencies:
  cus_theme: ^1.8.1
  provider: ^6.1.5
  shared_preferences: ^2.5.3
```

Then run:

```sh
flutter pub get
```

---

## Usage

### Basic Usage

```dart
import 'package:flutter/material.dart';
import 'package:cus_theme/cus_theme.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    // Create a theme instance (default is light)
    final appTheme = AppTheme();

    return MaterialApp(
      title: 'Custom Theme Demo',
      theme: appTheme.themeData,
      home: const MyHomePage(),
    );
  }
}

// For dark theme
// final darkTheme = AppTheme(isDarkMode: true);
// theme: darkTheme.themeData,
```

---

### Dynamic Theme with Provider

For dynamic theme switching and persistence, use the `ThemeProvider` and the helper:

```dart
import 'package:flutter/material.dart';
import 'package:cus_theme/cus_theme.dart';
import 'package:provider/provider.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return wrapWithThemeProvider(
      initialDarkMode: false,
      child: Consumer<ThemeProvider>(
        builder: (context, themeProvider, _) {
          return MaterialApp(
            title: 'Theme Provider Demo',
            theme: themeProvider.themeData,
            home: const HomePage(),
          );
        },
      ),
    );
  }
}

class HomePage extends StatelessWidget {
  const HomePage({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Theme Demo')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            Text(
              'Current theme: ${context.isDarkMode ? "Dark" : "Light"}',
              style: Theme.of(context).textTheme.titleLarge,
            ),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () => context.toggleTheme(),
              child: const Text('Toggle Theme'),
            ),
          ],
        ),
      ),
    );
  }
}
```

---

## Theme Constants

Use constants for spacing, sizing, and animation:

```dart
Container(
  margin: EdgeInsets.all(ThemeConstants.spacing16),
  padding: ThemeConstants.paddingMedium,
  decoration: BoxDecoration(
    borderRadius: BorderRadius.circular(ThemeConstants.radiusMedium),
    color: Colors.blue,
  ),
  child: Text('Hello World'),
);
```

---

## Theme Colors

Access color schemes and semantic colors:

```dart
Container(
  color: ThemeColors.primaryPurple,
  child: Text('Primary Purple'),
);

Container(
  color: CustomThemeExtension.of(context).success,
  child: Text('Success'),
);
```

---

## Typography

Use the built-in text styles and helpers:

```dart
Text('Headline Large', style: Theme.of(context).textTheme.headlineLarge);
Text('Bold', style: ThemeText.bold(Theme.of(context).textTheme.bodyMedium!));
Text('Colored', style: ThemeText.withColor(Theme.of(context).textTheme.bodyMedium!, Colors.red));
```

---

## Theme Extensions

Access extra theme properties and helpers:

```dart
final customTheme = CustomThemeExtension.of(context);

Container(
  decoration: customTheme.getCardDecoration(elevation: 2.0, isHovered: true),
  padding: customTheme.contentPadding,
  child: Text('Custom Card'),
);

Row(
  children: [
    CustomThemeExtension.createTag(context, 'Tag'),
    const SizedBox(width: 8),
    CustomThemeExtension.createBadge(context, '5'),
    const SizedBox(width: 8),
    CustomThemeExtension.createRating(context, 4.5),
  ],
);
```

---

## Theme Presets

Switch between built-in color presets:

```dart
ElevatedButton(
  onPressed: () => context.applyThemePreset('blue'),
  child: const Text('Blue Theme'),
);
```

---

## Customization

### Extending AppTheme

```dart
class MyCustomTheme extends AppTheme {
  MyCustomTheme({super.isDarkMode});

  @override
  ThemeData get _lightTheme {
    final baseTheme = super._lightTheme;
    return baseTheme.copyWith(
      appBarTheme: const AppBarTheme(
        centerTitle: true,
        elevation: 0,
      ),
      cardTheme: const CardTheme(
        clipBehavior: Clip.antiAlias,
        margin: EdgeInsets.symmetric(vertical: 8, horizontal: 16),
      ),
    );
  }
}
```

### Creating a Custom Theme Extension

```dart
class MyCustomExtension extends ThemeExtension<MyCustomExtension> {
  final Color specialColor;
  final BorderRadius specialRadius;

  MyCustomExtension({
    required this.specialColor,
    required this.specialRadius,
  });

  @override
  ThemeExtension<MyCustomExtension> copyWith({
    Color? specialColor,
    BorderRadius? specialRadius,
  }) {
    return MyCustomExtension(
      specialColor: specialColor ?? this.specialColor,
      specialRadius: specialRadius ?? this.specialRadius,
    );
  }

  @override
  ThemeExtension<MyCustomExtension> lerp(
    covariant ThemeExtension<MyCustomExtension>? other,
    double t,
  ) {
    if (other is! MyCustomExtension) return this;
    return MyCustomExtension(
      specialColor: Color.lerp(specialColor, other.specialColor, t)!,
      specialRadius: BorderRadius.lerp(specialRadius, other.specialRadius, t)!,
    );
  }

  static MyCustomExtension of(BuildContext context) {
    return Theme.of(context).extension<MyCustomExtension>()!;
  }
}
```

---

## API Reference

### Main Classes

- `AppTheme` – Main theme class, provides `ThemeData`
- `ThemeColors` – Color schemes and color utilities
- `ThemeText` – Typography and text style utilities
- `ThemeConstants` – Spacing, sizing, and timing constants
- `CustomThemeExtension` – Additional theme properties
- `ThemeProvider` – State management for dynamic theme changes

### Helper Functions

- `wrapWithThemeProvider` – Wraps your app with ThemeProvider
- `themeBuilder` – Rebuilds when theme changes
- `getAppTheme` – Gets the active AppTheme
- `getCurrentTheme` – Gets the current ThemeData
- `getCustomTheme` – Gets the custom theme extension

### Context Extensions

- `context.toggleTheme()` – Toggle between light and dark mode
- `context.setDarkMode(bool)` – Set dark mode explicitly
- `context.setPrimaryColor(Color?)` – Set primary color
- `context.setSecondaryColor(Color?)` – Set secondary color
- `context.applyThemePreset(String)` – Apply a theme preset
- `context.themeProvider` – Get the theme provider
- `context.theme` – Get current ThemeData
- `context.customTheme` – Get custom theme extension
- `context.isDarkMode` – Check if theme is dark mode

---

<!-- ## Support & Donation

If you find this package helpful, consider supporting its development:

[![Donate via Razorpay](https://img.shields.io/badge/Donate-Razorpay-blue.svg)](https://rzp.io/l/pl_Pz2rI1rs7TmNWv)

Your support helps keep this project active and maintained. Thank you!

--- -->

## Additional information

- For a complete example, see the [example](https://github.com/bluedev-in/cus_theme/tree/main/example) directory.
- This package is licensed under the Apache-2.0 License.
- Contributions are welcome! Please submit a Pull Request or open an issue.

---
