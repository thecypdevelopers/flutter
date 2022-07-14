# The Cyprinus - Flutter

-------------------------------------------------------------------------------

El módulo *más* sofisticado de **seguimiento de ubicación y geoperimetraje** en segundo plano con inteligencia de detección de movimiento consciente de la batería para **iOS** y **Android**.

**detección de movimiento** (usando acelerómetro, giroscopio y magnetómetro) para detectar cuando el dispositivo se *mueve* y *estacionario*.

- Cuando se detecta que el dispositivo **se está moviendo**, el complemento *automáticamente* comenzará a registrar una ubicación de acuerdo con el `distanceFilter` (metros) configurado.

- Cuando se detecta que el dispositivo está **estacionario**, el complemento desactivará automáticamente los servicios de ubicación para conservar energía.

----------------------------------------------------------------------------

The **[Android module](https://www.transistorsoft.com/shop/products/flutter-background-geolocation)** requires [purchasing a license](https://www.transistorsoft.com/shop/products/flutter-background-geolocation).  However, it *will* work for **DEBUG** builds.  It will **not** work with **RELEASE** builds [without purchasing a license](https://www.transistorsoft.com/shop/products/flutter-background-geolocation).

(2018) This plugin is supported **full-time** and field-tested **daily** since 2013.

----------------------------------------------------------------------------

[![Google Play](https://dl.dropboxusercontent.com/s/80rf906x0fheb26/google-play-icon.png?dl=1)](https://play.google.com/store/apps/details?id=com.transistorsoft.flutterbackgroundgeolocationexample)

![Home](https://dl.dropboxusercontent.com/s/wa43w1n3xhkjn0i/home-framed-350.png?dl=1)
![Settings](https://dl.dropboxusercontent.com/s/8oad228siog49kt/settings-framed-350.png?dl=1)

# Contents

- ### 📚 [API Documentation](https://pub.dartlang.org/documentation/flutter_background_geolocation/latest/flt_background_geolocation/flt_background_geolocation-library.html)
- ### [Instalación de Plugin](#-installing-the-plugin)
- ### [Setup](#-setup-guides)
- ### [Uso del plugin](#-using-the-plugin)
- ### [Ejemplo](#l-example)
- ### [Aplcación Demo Application](#-demo-application)
- ### [Conexión al servidor de prueba](#-simple-testing-server)

## 🔷 Instalación de Plugin

📂 **`pubspec.yaml`**:

**Note:** See [Versions](https://pub.dartlang.org/packages/flutter_background_geolocation/#-versions-tab-) for latest available version.

```yaml
dependencies:
  flutter_background_geolocation: '^1.9.0'
```

### Or latest from Git:

```yaml
dependencies:
  flutter_background_geolocation:
    git:
      url: https://github.com/transistorsoft/flutter_background_geolocation.git
```

## 🔷 Setup

- [iOS](https://github.com/transistorsoft/flutter_background_geolocation/blob/master/help/INSTALL-IOS.md)
- [Android](https://github.com/transistorsoft/flutter_background_geolocation/blob/master/help/INSTALL-ANDROID.md)


## 🔷 Uso de plugin ##

```dart
import 'package:flutter_background_geolocation/flutter_background_geolocation.dart' as bg;
```

⚠️ Note `as bg` in the `import`.  This is important to namespace the plugin's classes, which often use common class-names such as `Location`, `Config`, `State`.  You will access every `flutter_background_geolocation` class with the prefix `bg` (ie: "**b**ackground **g**eolocation").

## 🔷 Ejemplo
[Full Example](https://gist.github.com/christocracy/a0464846de8a9c27c7e9de5616082878)

There are three main steps to using `BackgroundGeolocation`:

1. Wire up event-listeners.
2. Configure the plugin with `#ready`.
3. `#start` the plugin.

```dart

import 'package:flutter_background_geolocation/flutter_background_geolocation.dart' as bg;

class _MyHomePageState extends State<MyHomePage> {

  @override
  void initState() {
    super.initState();

    ////
    // 1.  Listen to events (See docs for all 12 available events).
    //

    // Fired whenever a location is recorded
    bg.BackgroundGeolocation.onLocation((bg.Location location) {
      print('[location] - $location');
    });

    // Fired whenever the plugin changes motion-state (stationary->moving and vice-versa)
    bg.BackgroundGeolocation.onMotionChange((bg.Location location) {
      print('[motionchange] - $location');
    });

    // Fired whenever the state of location-services changes.  Always fired at boot
    bg.BackgroundGeolocation.onProviderChange((bg.ProviderChangeEvent event) {
      print('[providerchange] - $event');
    });

    ////
    // 2.  Configure the plugin
    //
    bg.BackgroundGeolocation.ready(bg.Config(
        desiredAccuracy: bg.Config.DESIRED_ACCURACY_HIGH,
        distanceFilter: 10.0,
        stopOnTerminate: false,
        startOnBoot: true,
        debug: true,
        logLevel: bg.Config.LOG_LEVEL_VERBOSE
    )).then((bg.State state) {
      if (!state.enabled) {
        ////
        // 3.  Start the plugin.
        //
        bg.BackgroundGeolocation.start();
      }
    });
  }
}

```

⚠️ Do not execute *any* API method which will require accessing location-services until the callback to **`#ready*` executes (eg: `#getCurrentPosition`, `#watchPosition`, `#start`).


## 🔷 Aplicación Demo

Una aplicación de demostración con todas las funciones está disponible. Esta aplicación de demostración incluye una pantalla de configuración que le permite experimentar rápidamente con todas las diferentes configuraciones disponibles para cada plataforma. Solicitar a The Cyprinus.    
    

![Home](https://dl.dropboxusercontent.com/s/wa43w1n3xhkjn0i/home-framed-350.png?dl=1)
![Settings](https://dl.dropboxusercontent.com/s/8oad228siog49kt/settings-framed-350.png?dl=1)

## 🔷 Conexión al servidor de prueba

Solicitar a The Cyprinus
