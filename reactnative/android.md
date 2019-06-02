reactnative android

# Getting started

Look for the android cli instructions here: https://facebook.github.io/react-native/docs/getting-started

## Java Development Kit (JDK)

Now that Java 8 is no longer the most current version, if you want Java 8 install it this way:

    brew tap caskroom/versions
    brew cask install java8

Verify

    javac -version
    # javac 1.8.0_202

## Android Studio

Use _Custom_ installation and make sure the boxes next the following are checked:

    Performance (Intel ® HAXM) (See here for AMD)
    Android Virtual Device

During the install you will be asked to go to `Security and Privacy` in the _System Preferences_ app and allow the _HAXM_ extension.

    Running Intel® HAXM installer

Just wait 2-3 minutes and you should see it progressing and finishing installation

    Running Intel® HAXM installer
    Silent installation Pass!
    ...

From the welcome screen to go `Configure` and select the "SDK Platforms" tab from within the SDK Manager, then check the box next to "Show Package Details" in the bottom right corner. Look for and expand the `Android 9 (Pie)` entry, then make sure the following items are checked:

    Android SDK Platform 28
    Google APIs Intel x86 Atom System Image

Next, select the "SDK Tools" tab and check the box next to _Show Package Details_ here as well. Look for and expand the "Android SDK Build-Tools" entry, then make sure that 28.0.3 is selected.

Add the following lines to your `$HOME/.bash_profile` config file:

    export ANDROID_HOME=$HOME/Library/Android/sdk
    export PATH=$PATH:$ANDROID_HOME/emulator
    export PATH=$PATH:$ANDROID_HOME/tools
    export PATH=$PATH:$ANDROID_HOME/tools/bin
    export PATH=$PATH:$ANDROID_HOME/platform-tools

# Running

    $ emulator -list-avds
    # Nexus_5X_API_28_x86
    emulator @Nexus_5X_API_28_x86
    npx react-native run-android

To access the developer menu within your app in the Android Emulator press `Command + m` on OSX or `Ctrl + m` on Linux/Windows.


# Releasing

## First time
    keytool -genkey -v -keystore app/staging.keystore -alias stagingkey -keyalg RSA -keysize 2048 -validity 10000

## Everytime

    ./gradlew assembleRelease

## Alternative/additional instructions

1. Increment "versionCode" in "android/app/build.gradle"
2. Run `cd android && ./gradlew app:assembleRelease -x lintVitalRelease`
3. Go to "android/app/build/outputs/apk" and pick the "app-release.apk -x lintVitalRelease"
4. Upload to App Center

## Troubleshooting

“error: uncompiled PNG file passed as argument. Must be compiled first into .flat file..
error: failed parsing overlays.”
https://lengerrong.blogspot.pt/2018/01/react-natvie-run-android-variantrelease.html
