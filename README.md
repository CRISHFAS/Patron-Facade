# Implementación del Patrón Facade en Java

![Java](https://img.shields.io/badge/Java-17-blue)
![License](https://img.shields.io/badge/License-MIT-green)

El **Patrón Facade** es un patrón de diseño estructural que proporciona una interfaz simplificada para interactuar con sistemas o bibliotecas complejas. Se utiliza para ocultar la lógica interna de las clases y ofrecer una forma más fácil y coherente de trabajar con ellas.


## Características principales

- 🌟 **Simplicidad:** Una única interfaz facilita el uso de subsistemas complejos.
- 🔒 **Encapsulación:** Oculta los detalles internos de las clases, dejando expuesta una funcionalidad simple.
- 🔄 **Modularidad:** Separa al cliente de la implementación interna, permitiendo cambios en el subsistema sin afectar el código cliente.

## Arquitectura general

### Estructura del patrón Facade

1. **Subsistema complejo**
   - Contiene las clases que realizan las tareas específicas (en este caso, conversión de video y ajustes de audio).

2. **Fachada (Facade)**
   - Proporciona una interfaz única para simplificar la interacción con el subsistema.

3. **Código cliente**
   - Consume la fachada, sin interactuar directamente con las clases del subsistema.

### Diagrama de clases

```Mermaid
graph TD;
    Cliente --> Facade --> Subsistema_complejo
    Subsistema_complejo -->|Clase| VideoFile
    Subsistema_complejo -->|Clase| CodecFactory
    Subsistema_complejo -->|Clase| BitrateReader
    Subsistema_complejo -->|Clase| AudioMixer
```    


## Implementación en Java

1. Subsistema complejo

El subsistema está compuesto por varias clases que realizan tareas específicas.

**VideoFile.java**

```java
package media_library;

public class VideoFile {
    private String name;
    private String codecType;

    public VideoFile(String name) {
        this.name = name;
        this.codecType = name.substring(name.indexOf(".") + 1);
    }

    public String getCodecType() {
        return codecType;
    }

    public String getName() {
        return name;
    }
}
```

**Codec.java**

```java
package media_library;

public interface Codec {
}
MPEG4CompressionCodec.java
java
package media_library;

public class MPEG4CompressionCodec implements Codec {
    public String type = "mp4";
}
```

**OggCompressionCodec.java**

```java
package media_library;

public class OggCompressionCodec implements Codec {
    public String type = "ogg";
}
```

- `CodecFactory`: Detecta el códec de origen.

```java
package media_library;

public class CodecFactory {
    public static Codec extract(VideoFile file) {
        String type = file.getCodecType();
        if (type.equals("mp4")) {
            System.out.println("CodecFactory: extracting mpeg audio...");
            return new MPEG4CompressionCodec();
        } else {
            System.out.println("CodecFactory: extracting ogg audio...");
            return new OggCompressionCodec();
        }
    }
}
```

- `BitrateReader`: Convierte el video al formato de destino.

```java
package media_library;

public class BitrateReader {
    public static VideoFile read(VideoFile file, Codec codec) {
        System.out.println("BitrateReader: reading file...");
        return file;
    }

    public static VideoFile convert(VideoFile buffer, Codec codec) {
        System.out.println("BitrateReader: writing file...");
        return buffer;
    }
}
```

- `AudioMixer`: Ajusta la pista de audio.

```java
package media_library;

import java.io.File;

public class AudioMixer {
    public File fix(VideoFile result) {
        System.out.println("AudioMixer: fixing audio...");
        return new File("tmp");
    }
}
```

2. Clase Facade

La fachada proporciona una interfaz única para simplificar el acceso al subsistema.

**VideoConversionFacade.java**

```java
package facade;

import media_library.*;

import java.io.File;

public class VideoConversionFacade {
    public File convertVideo(String fileName, String format) {
        System.out.println("VideoConversionFacade: conversion started.");
        VideoFile file = new VideoFile(fileName);
        Codec sourceCodec = CodecFactory.extract(file);
        Codec destinationCodec;
        if (format.equals("mp4")) {
            destinationCodec = new MPEG4CompressionCodec();
        } else {
            destinationCodec = new OggCompressionCodec();
        }
        VideoFile buffer = BitrateReader.read(file, sourceCodec);
        VideoFile intermediateResult = BitrateReader.convert(buffer, destinationCodec);
        File result = (new AudioMixer()).fix(intermediateResult);
        System.out.println("VideoConversionFacade: conversion completed.");
        return result;
    }
}
```

3. Código cliente

El cliente utiliza la fachada para realizar las operaciones, sin necesidad de interactuar directamente con el subsistema.

**Demo.java**

```java
package example;

import facade.VideoConversionFacade;

import java.io.File;

public class Demo {
    public static void main(String[] args) {
        VideoConversionFacade converter = new VideoConversionFacade();
        File mp4Video = converter.convertVideo("youtubevideo.ogg", "mp4");
        System.out.println("Video convertido a formato mp4: " + mp4Video.getName());
    }
}
```

## Resultados de la ejecución

Cuando compilas y ejecutas el programa, obtienes la siguiente salida:

```plaintext
VideoConversionFacade: conversion started.
CodecFactory: extracting ogg audio...
BitrateReader: reading file...
BitrateReader: writing file...
AudioMixer: fixing audio...
VideoConversionFacade: conversion completed.
Video convertido a formato mp4: tmp
```

## Beneficios del patrón Facade
- 🌟 **Simplificación:** Facilita la interacción con sistemas complejos ofreciendo una interfaz única.
- 🔒 **Encapsulación:** Oculta los detalles internos del subsistema y los expone de forma consistente.
- 🔄 **Flexibilidad:** Permite cambiar o extender el subsistema sin modificar el código cliente.
- ♻️ **Reutilización:** El Facade puede ser reutilizado en diferentes clientes o proyectos.

### Ejemplo práctico

- Entrada del cliente: youtubevideo.ogg

- Proceso interno:

- CodecFactory detecta el códec de origen.

- BitrateReader convierte el video al formato de destino.

- AudioMixer ajusta la pista de audio.

- Salida: Video en formato mp4.

## Problemas frecuentes
- **Error:** `Class not found: example.Demo`
  - Asegúrate de compilar todos los archivos y que la estructura de carpetas coincida con los paquetes.
- **Error:** `No such file or directory: src/...`
  - Confirma que los archivos están ubicados correctamente y usa rutas relativas al compilar.


### Conclusión

El patrón Facade es una solución ideal cuando necesitas simplificar la interacción con sistemas complejos o bibliotecas. En este proyecto, fue usado para encapsular la lógica de conversión de video, haciendo que el cliente solo tenga que interactuar con la fachada sin preocuparse por los detalles internos. Esto permite escalabilidad, modularidad y facilidad de mantenimiento en sistemas grandes.

Lea más acerca del [Patrón Facade y las interfaces de grano grueso para sistemas distribuidos.](https://pearblog.hashnode.dev/fachada-remota-interfaces-de-grano-grueso-para-sistemas-distribuidos)

