Hier wollen wir euch zeigen, wie ihr mit LMS ein Live-Bild eurer Webcam bekommt. Wenn ihr keine Webcam habt, dann könnt
ihr auch eine beliebige Cam per USB abschließen, solange sie von Linux unterstützt wird.

### 1. Ein Config Repo erstellen
Die Basis eurer Arbeit wird stets ein Config Repo, denn dort werden das LMS Framework, eure Module und Libraries sowie Config Dateien gebündelt abgelegt und gemeinsam versioniert. Dazu klont ihr zunächst unsere Blaupause config_blueprint:

```
git clone https://github.com/lms-org/config_blueprint.git
```

Nun gebt dem Config Repo einen sprechenden Namen.

```
mv config_blueprint config_tutorial
```

Im Config Repo ist bereits LMS als Submodul hinzufügt, allerdings ist die Version veraltet, also müssen wir nun updaten:

```
cd config_tutorial
git submodule init
git submodule update
cd LMS
git checkout master
cd ..
```

Das eben heruntergeladene Projekt ist vollkommen leer und möchte nun von euch befüllt werden.

### 2. Eine Library herunterladen
Wenn ihr irgendwas mit Bildern machen wollt, so braucht ihr unsere [imaging lib](https://github.com/lms-org/imaging) und
die [math lib](https://github.com/lms-org/math_lib).

Geht in den libraries Ordner und erstellt ein Git-Submodul. Git kümmert sich um den Rest.

```sh
cd external/libraries
git submodule add https://github.com/lms-org/imaging.git
git submodule add https://github.com/lms-org/math_lib.git
cd ../..
```

### 3. Ein Module herunterladen
Nun könnt ihr ein Modul herunterladen. Das funktioniert ganz ähnlich wie bei den Libraries.

```
cd external/modules
git submodule add https://github.com/lms-org/camera_importer.git
git submodule add https://github.com/lms-org/image_converter.git
git submodule add https://github.com/lms-org/sdl_image_renderer.git
cd ../..
```

### 4. Die Services
Füge nun auch noch einen Service hinzu.

```
cd external/services
git submodule add https://github.com/lms-org/sdl_service.git
cd ../..
```

### 5. Konfigurieren

Erstellt eine Datei `framework_conf.xml` im configs Ordner und fügt folgenden
Inhalt ein:

```xml
<framework>
    <execution>
        <mainThread />
    </execution>
    <modulesToEnable logLevel="WARN">
        <module>camera_importer</module>
        <module>image_converter</module>
        <module>sdl_image_renderer</module>
    </modulesToEnable>
    <module>
        <name>camera_importer</name>
        <config>
            <device>/dev/video0</device>
            <width>320</width>
            <height>240</height>
            <format>YUYV</format>
            <framerate>30</framerate>
        </config>
    </module>
    <module>
        <name>image_converter</name>
        <channelHint name="INPUT_IMAGE" mapTo="IMAGE" />
        <channelHint name="OUTPUT_IMAGE" mapTo="COLOR_IMAGE" />
        <config>
            <output_format>RGB</output_format>
        </config>
    </module>
    <module>
        <name>sdl_image_renderer</name>
        <config>
            <title>Webcam</title>
            <channels>COLOR_IMAGE</channels>
        </config>
    </module>
    <service>
        <name>sdl_service</name>
    </service>
</framework>
```

### 6. Kompilieren
Erstellt einen Build-Ordner, startet dort CMake und kompiliert dann. Das kann eine ganze Weile dauern.

```
mkdir build
cd build
cmake ..
make -j4
```

### 7. Starten
Bleibt im Build-Ordner startet LMS:

```
./lms
```

Falls du keine Lust mehr hast, deine eigene Visage zu betrachten, dann drücke `Strg+C` im Terminal, um LMS zu beenden.
