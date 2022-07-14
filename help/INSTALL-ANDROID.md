# Android Installation

## `AndroidManifest`

Flutter seems to have a problem with 3rd-party Android libraries which merge their own `AndroidManifest.xml` into the application, particularly the `android:label` attribute.

##### :open_file_folder: `android/app/src/main/AndroidManifest.xml`:

```diff
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
+   xmlns:tools="http://schemas.android.com/tools"
    package="com.example.helloworld">

    <application
+        tools:replace="android:label"
         android:name="io.flutter.app.FlutterApplication"
         android:label="flutter_background_geolocation_example"
         android:icon="@mipmap/ic_launcher">
</manifest>

```

##### :warning: Failure to perform the step above will result in a **build error**

```
Execution failed for task ':app:processDebugManifest'.
> Manifest merger failed : Attribute application@label value=(hello_world) from AndroidManifest.xml:17:9-36
    is also present at [tslocationmanager-2.13.3.aar] AndroidManifest.xml:24:18-50 value=(@string/app_name).
    Suggestion: add 'tools:replace="android:label"' to <application> element at AndroidManifest.xml:15:5-38:19 to override.
```


## `android/gradle.properties`

Ensure your app is [migrated to use AndroidX](https://flutter.dev/docs/development/packages-and-plugins/androidx-compatibility).

:open_file_folder: `android/gradle.properties`:

```diff
org.gradle.jvmargs=-Xmx1536M
+android.enableJetifier=true
+android.useAndroidX=true

```

## `android/build.gradle`

As an app grows in complexity and imports a variety of 3rd-party modules, it helps to provide some key **"Global Gradle Configuration Properties"** which all modules can align their requested dependency versions to.  `flutter_background_geolocation` **is aware** of these variables and will align itself to them when detected.  One of the most common build errors comes from multiple 3rd-party modules importing different version of `play-services` or `support` libraries.

:open_file_folder: `android/build.gradle`:

```diff
buildscript {
    ext.kotlin_version = '1.3.0' // Must use 1.3.0 OR HIGHER
+   ext {
+       compileSdkVersion   = 31                // or higher
+       targetSdkVersion    = 31                // or higher
+       appCompatVersion    = "1.1.0"           // or higher
+       playServicesLocationVersion = "19.0.1"  // or higher
+   }

    repositories {
        google()
        mavenCentral()
    }

    dependencies {
         // Of ALREADY HIGHER than 3.3.1, DO NOT CHANGE
+        classpath 'com.android.tools.build:gradle:3.3.1' // Must use 3.3.1 or higher
    }
}

allprojects {
    repositories {
        google()
        mavenCentral()
+       maven {
+           // [required] flutter_background_geolocation
+           url "${project(':flutter_background_geolocation').projectDir}/libs"
+       }
+       maven {
+           // [required] background_fetch
+           url "${project(':background_fetch').projectDir}/libs"
+       }
    }
}

```

## `android/app/build.gradle`

In addition, you should take advantage of the *Global Configuration Properties* **yourself**, replacing hard-coded values in your `android/app/build.gradle` with references to these variables:

:open_file_folder: `android/app/build.gradle`:

```diff
apply plugin: 'com.android.application'
apply from: "$flutterRoot/packages/flutter_tools/gradle/flutter.gradle"

// flutter_background_geolocation (must be placed after the lines above)
+Project background_geolocation = project(':flutter_background_geolocation')
+apply from: "${background_geolocation.projectDir}/background_geolocation.gradle"

android {
+   compileSdkVersion rootProject.ext.compileSdkVersion
    .
    .
    .
    defaultConfig {
        .
        .
        .
+       targetSdkVersion rootProject.ext.targetSdkVersion
    }
    buildTypes {
        release {
            .
            .
            .
            minifyEnabled true
+           shrinkResources false
            // background_geolocation requires custom Proguard Rules with minifyEnabled
+           proguardFiles "${background_geolocation.projectDir}/proguard-rules.pro"
        }
    }
}

# Ensure AndroidX compatibility
dependencies {
     testImplementation 'junit:junit:4.12'
-    androidTestImplementation 'com.android.support.test:runner:1.0.2'
-    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
+    androidTestImplementation 'androidx.test:runner:1.1.1'                   // or higher
+    androidTestImplementation 'androidx.test.espresso:espresso-core:3.1.1'   // or higher
}

```

## Android 10 and *When in Use* Location Authorization

Android 10 introduces *When in Use* location authorization.  If you're building with `compileSdkVersion 29`, add the following elements to your **`AndroidManifest.xml`**.  This allows your app to continue location-tracking when location-services are initiated while your app is in the foreground.  For example:

```javascript
onClickStartTracking() {
    // Initiate tracking while app is in foreground.
    BackgroundGeolocation.changePace(true);
}
```

```diff
<manifest>
    <application>
+       <service android:name="com.transistorsoft.locationmanager.service.TrackingService" android:foregroundServiceType="location" />
+       <service android:name="com.transistorsoft.locationmanager.service.LocationRequestService" android:foregroundServiceType="location" />
    </application>
</manifest>
```

## Configure your license

The cyprinus enviará la licencia

2. Add your license-key to `android/app/src/main/AndroidManifest.xml`:

```diff
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.your.package.id">

  <application>
    <!-- flutter_background_geolocation licence -->
+     <meta-data android:name="com.thecyprinus.locationmanager.license" android:value="YOUR_LICENCE_KEY_HERE" />
    .
    .
    .
  </application>
</manifest>
```

## Android Headless Mode with `enableHeadless: true`

If you intend to respond to the BackgroundGeolocation SDK's events with your own `dart` callback while your **app is terminated**, that is *Headless*, See [Android Headless Mode](../../../wiki/Android-Headless-Mode) and perform those additional steps now.

```dart
BackgroundGeolocation.ready(Config(
  enableHeadless: true  // <--
));
```

## `background_fetch`

`flutter_background_geolocation` installs a dependency `background_fetch`.  You can optionally perform the [Android Setup](https://github.com/thecypdevelopers/flutter/blob/master/help/INSTALL-ANDROID.md) for it as well.

