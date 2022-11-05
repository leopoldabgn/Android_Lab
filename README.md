# Android_Lab

## Trouver le nom du package
    adb shell pm list packages
    adb shell pm list packages | grep nomApp

## Extraire l'apk du téléphone
    adb shell pm path com.mon.package

    adb pull /data/app/com.example.someapp-2.apk path/to/desired/destination

## Decompile/Recompile the app – see the source code
```bash
# Decompiler l'apk
apktool d -s app.apk -o decoded
# -s do not decode resources
# -o output directory

# Decompiler l'apk ??
~/dex-tools-2.2-SNAPSHOT/d2j-dex2jar.sh -f myApp.apk

# Voir le code décompilé
jd-gui-1.6.6.jar &

# Recompiler
# Now, repack with -d option, that will automatically add debuggable="true" to the AndroidManifest.xml:
apktool b decoded -d -o myApp.apk

# where b stands for build.  

# Option -d is very useful, as one would have to alter AndroidManifest.xml manually to add app:debuggable=true attribute to <application> tag.

```

## Attach the debugger

Run the app in “wait for debugger” mode:
```
adb shell am start -D -n "com.mon.package/blablabla.MainActivity"
```
where **am** is an ActivityManager;

**start** starts a component;

    -D enables debugging;

Get the **PID** of your app :
```
adb jdwp

adb shell ps | grep <appli_package>
```
Ensuite :
```
adb forward tcp:8700 jdwp:<PID>
```

Puis on se connecte sur le port avec jdb:
```
jdb -attach 8700
```

## Sign APK

### apksigner
The app needs to be signed, otherwise the installation of the unsigned app will fail. Use apksigner to check whether the app will pass the verification process during installation:
```
apksigner verify --print-certs --verbose <path_to_apk>
```
The following template can be used to sign the app:
```
apksigner sign -v --in UnCrackable-Level1-repackaged-with-d-option.apk --v2-signing-enabled --ks <path_to_keystore_file> --ks-key-alias $KEYSTORE_KEY_ALIAS --ks-pass env:KEYSTORE_PASSWORD --ks-type pkcs12
```

### key-tool && jarsigner
Fichier bash qui prend en parametre le nom de l'apk à signer. Il génère une clé avec *keytool* puis signe l'apk avec *jarsigner*
```bash
#!/bin/bash
keytool -genkey -v -keystore my-release-key.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore my-release-key.keystore "$1" alias_name
```

## Installer/desintaller une appli
    adb install appli.apk
    adb uninstall com.mon.package.appli

## Commands

### apk-tool

### Java Decompiler

#### jd-gui
Permet de decompiler un *.class* et de l'afficher dans une interface graphique.
Lien: http://java-decompiler.github.io/#jd-online

## The tools
```bash
adb – install apk, get shell
apktool – unpack/re-package apk
dex2jar – convert .dex files to .class files
jd-gui – java decompiler with GUI
jdb – Java debugger
gdb – GNU project debugger
cutter – binary patching with GUI
radare2 – binary patching
```

## Interessant
https://developers.mews.com/cracking-uncrackable-android-apps-level-1/

## Download
dex2jar:
https://github.com/pxb1988/dex2jar/releases

jd-gui:
http://java-decompiler.github.io/

apktool:
https://bitbucket.org/iBotPeaches/apktool/downloads/