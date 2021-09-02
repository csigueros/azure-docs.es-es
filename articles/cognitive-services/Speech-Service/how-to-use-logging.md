---
title: 'Registro del SDK de voz: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo habilitar el registro en el SDK de Voz (C++, C#, Python, Objective-C, Java).
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: amishu
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 584e200beac484ea742d51341a9cb93f0cfc4a41
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745646"
---
# <a name="enable-logging-in-the-speech-sdk"></a>Habilitar el registro en el SDK de voz

El registro en un archivo es una característica opcional del SDK de voz. Durante el desarrollo, el registro proporciona información adicional y diagnósticos de los componentes principales del SDK de voz. Se puede habilitar estableciendo la propiedad `Speech_LogFilename` de un objeto de configuración de voz en la ubicación y el nombre del archivo de registro. El registro se controla mediante una clase estática en la biblioteca nativa del SDK de voz. Puede activar el registro para cualquier instancia de reconocedor o sintetizador del SDK de voz. Todas las instancias del mismo proceso escriben entradas de registro en el mismo archivo de registro.

> [!NOTE]
> El registro está disponible desde la versión 1.4.0 del SDK de voz en todos los lenguajes de programación admitidos a excepción de JavaScript.

## <a name="sample"></a>Muestra

El nombre del archivo de registro se especifica en un objeto de configuración. Tomando `SpeechConfig` como ejemplo y suponiendo que ha creado una instancia denominada `config`:

```csharp
config.SetProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```java
config.setProperty(PropertyId.Speech_LogFilename, "LogfilePathAndName");
```

```C++
config->SetProperty(PropertyId::Speech_LogFilename, "LogfilePathAndName");
```

```Python
config.set_property(speechsdk.PropertyId.Speech_LogFilename, "LogfilePathAndName")
```

```objc
[config setPropertyTo:@"LogfilePathAndName" byId:SPXSpeechLogFilename];
```

```go
import ("github.com/Microsoft/cognitive-services-speech-sdk-go/common")

config.SetProperty(common.SpeechLogFilename, "LogfilePathAndName")
```

Puede crear un reconocedor a partir del objeto de configuración. Esto habilitará el registro de todos los reconocedores.

> [!NOTE]
> Si crea un `SpeechSynthesizer` a partir del objeto de configuración, no se habilitará el registro. No obstante, si el registro está habilitado, también recibirá los diagnósticos desde `SpeechSynthesizer`.

## <a name="create-a-log-file-on-different-platforms"></a>Creación de un archivo de registro en distintas plataformas

Para Windows o Linux, el archivo de registro puede estar en cualquier ruta sobre la que el usuario tenga permiso de escritura. Los permisos de escritura en ubicaciones del sistema de archivos de otros sistemas operativos puede estar limitada o restringida de forma predeterminada.

### <a name="universal-windows-platform-uwp"></a>Plataforma universal de Windows (UWP)

Las aplicaciones de UWP requieren que los archivos de registro estén colocados en una de las ubicaciones de datos de la aplicación (local, roaming o temporal). Se puede crear un archivo de registro en la carpeta de la aplicación local:

```csharp
StorageFolder storageFolder = ApplicationData.Current.LocalFolder;
StorageFile logFile = await storageFolder.CreateFileAsync("logfile.txt", CreationCollisionOption.ReplaceExisting);
config.SetProperty(PropertyId.Speech_LogFilename, logFile.Path);
```

En una aplicación de Unity UWP, se puede crear un archivo de registro mediante la carpeta de la ruta de acceso de los datos persistentes de la aplicación como se indica a continuación:

```csharp
#if ENABLE_WINMD_SUPPORT
    string logFile = Application.persistentDataPath + "/logFile.txt";
    config.SetProperty(PropertyId.Speech_LogFilename, logFile);
#endif
```
Para más información sobre los permisos de acceso a archivos en aplicaciones para UWP, consulte [Permisos de acceso a archivos](/windows/uwp/files/file-access-permissions).

### <a name="android"></a>Android

Puede guardar un archivo de registro en un almacenamiento interno, externo o en el directorio de la memoria caché. Los archivos creados en el almacenamiento interno o en el directorio de la memoria caché son privados para la aplicación. Es preferible crear un archivo de registro en el almacenamiento externo.

```java
File dir = context.getExternalFilesDir(null);
File logFile = new File(dir, "logfile.txt");
config.setProperty(PropertyId.Speech_LogFilename, logFile.getAbsolutePath());
```

El código anterior guarda un archivo de registro en el almacenamiento externo de la raíz de un directorio específico de la aplicación. Un usuario puede acceder al archivo con el administrador de archivos (normalmente en `Android/data/ApplicationName/logfile.txt`). El archivo se eliminará cuando se desinstale la aplicación.

También deberá solicitar el permiso `WRITE_EXTERNAL_STORAGE` sobre el archivo de manifiesto:

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" package="...">
  ...
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  ...
</manifest>
```

En una aplicación de Unity Android, el archivo de registro se puede crear mediante la carpeta de la ruta de acceso de los datos persistentes de la aplicación como se indica a continuación:

```csharp
string logFile = Application.persistentDataPath + "/logFile.txt";
config.SetProperty(PropertyId.Speech_LogFilename, logFile);
```
Además, también debe establecer el permiso de escritura en la configuración del reproductor de Unity para Android en "Externo (SDCard)". El registro se escribirá en un directorio al que se puede acceder mediante una herramienta como Device File Explorer de Android Studio. La ruta de acceso exacta del directorio puede variar de un dispositivo Android a otro; la ubicación suele ser el directorio `sdcard/Android/data/your-app-packagename/files`.

[Aquí](https://developer.android.com/guide/topics/data/data-storage.html) hay disponible más información sobre almacenamiento de datos y de archivos para aplicaciones de Android.

#### <a name="ios"></a>iOS

Solo los directorios dentro del espacio aislado de la aplicación son accesibles. Los archivos se pueden crear en los directorios de documentos, biblioteca y temporales. Los archivos del directorio de documentos pueden estar a disposición de un usuario. 

Si usa Objective-C en iOS, utilice el siguiente fragmento de código para crear un archivo de registro en el directorio de documentos de la aplicación:

```objc
NSString *filePath = [
  [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
    stringByAppendingPathComponent:@"logfile.txt"];
[speechConfig setPropertyTo:filePath byId:SPXSpeechLogFilename];
```

Para acceder a un archivo creado, agregue las siguientes propiedades a la lista de propiedades `Info.plist` de la aplicación:

```xml
<key>UIFileSharingEnabled</key>
<true/>
<key>LSSupportsOpeningDocumentsInPlace</key>
<true/>
```

Si usa Swift en iOS, utilice el siguiente fragmento de código para habilitar los registros:
```swift
let documentsDirectoryPathString = NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true).first!
let documentsDirectoryPath = NSURL(string: documentsDirectoryPathString)!
let logFilePath = documentsDirectoryPath.appendingPathComponent("swift.log")
self.speechConfig!.setPropertyTo(logFilePath!.absoluteString, by: SPXPropertyId.speechLogFilename)
```

[Aquí](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html) hay más información disponible sobre el sistema de archivos de iOS.

## <a name="logging-with-multiple-recognizers"></a>Registro con varios reconocedores

Aunque se especifica una ruta de acceso de salida del archivo de registro como una propiedad de configuración en `SpeechRecognizer` u otro objeto SDK, el registro del SDK es una instalación singleton *para todo el proceso* sin concepto de instancias individuales. Puede considerar que el constructor `SpeechRecognizer` (o uno similar) llama implícitamente a una rutina "Configurar registro global" estática e interna con los datos de propiedad disponibles en el `SpeechConfig` correspondiente.

Esto significa que, por ejemplo, no se pueden configurar seis reconocedores paralelos para generar simultáneamente seis archivos independientes. En su lugar, el reconocedor más reciente creado configurará la instancia de registro global para que se emita en el archivo especificado en sus propiedades de configuración y todo el registro del SDK se emitirá en ese archivo.

Esto también significa que la duración del objeto que configuró el registro no está asociada a la duración del registro. El registro no se detendrá en respuesta al lanzamiento de un objeto SDK y continuará siempre que no se proporcione ninguna configuración de registro nueva. Una vez que el registro de todo el proceso se haya iniciado, para detenerlo es preciso establecer la ruta de acceso del archivo de registro en una cadena vacía al crear un objeto.

Para reducir la posibilidad de que se produzca una confusión al configurar el registro de varias instancias, es posible que sea útil abstraer el control del registro de objetos que realizan trabajo real. Un par de rutinas auxiliares de ejemplo:

```cpp
void EnableSpeechSdkLogging(const char* relativePath)
{
    auto configForLogging = SpeechConfig::FromSubscription("unused_key", "unused_region");
    configForLogging->SetProperty(PropertyId::Speech_LogFilename, relativePath);
    auto emptyAudioConfig = AudioConfig::FromStreamInput(AudioInputStream::CreatePushStream());
    auto temporaryRecognizer = SpeechRecognizer::FromConfig(configForLogging, emptyAudioConfig);
}

void DisableSpeechSdkLogging()
{
    EnableSpeechSdkLogging("");
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Exploración de ejemplos en GitHub](https://aka.ms/csspeech/samples)
