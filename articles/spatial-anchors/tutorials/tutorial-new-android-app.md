---
title: 'Tutorial: Crear una nueva aplicación Android'
description: En este tutorial, aprenderá a crear una aplicación Android mediante Azure Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: e88654f0dc118fa09d6c1d83287519f3cdbe7363
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122229071"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-android-app-using-azure-spatial-anchors"></a>Tutorial: instrucciones detalladas para crear una aplicación Android mediante Azure Spatial Anchors

Este tutorial le mostrará cómo crear una aplicación Android que integra la funcionalidad ARCore con Azure Spatial Anchors.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de disponer de los siguientes elementos:

- Una máquina Windows o macOS con <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.4 o superior</a>.
- Un dispositivo Android <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">habilitado para el desarrollo</a> y <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">preparado para ARCore</a>.

## <a name="getting-started"></a>Introducción

Inicie Android Studio. En la ventana **Welcome to Android Studio** (Introducción a Android Studio), haga clic en **Start a new Android Studio project** (Iniciar un nuevo proyecto de Android Studio). 
1. Seleccione **Archivo**->**Nuevo proyecto**.
1. En la ventana **Create New Project** (Crear proyecto), en la sección **Phone y Tablet** (Teléfono y tableta), elija **Empty Activity** (Actividad vacía) y haga clic en **Next** (Siguiente).
1. En la ventana New Project - Empty Activity (Nuevo proyecto > Actividad vacía), cambie los valores siguientes:
   - Cambie el **nombre** y el **nombre del paquete** por los valores deseados y **guarde la ubicación**.
   - Establezca el **lenguaje** en `Java`.
   - Establezca el **nivel mínimo de la API** en `API 26: Android 8.0 (Oreo)`.
   - Deje las restantes opciones como están.
   - Haga clic en **Finalizar** 
1. Se ejecutará **Component Installer** (Instalador de componentes). Tras un tiempo de procesamiento Android Studio abrirá el IDE.

![Android Studio: nuevo proyecto](../../../includes/media/spatial-anchors-androidstudio/androidstudio-newproject.png)

<!-- maybe snapshot here -->

## <a name="trying-it-out"></a>Prueba

Para probar la nueva aplicación, conecte el dispositivo habilitado para desarrolladores en el equipo de desarrollo con un cable USB. En la parte superior derecha de Android Studio seleccione el dispositivo conectado y haga clic en el icono **Ejecutar "aplicación"** . Android Studio instala la aplicación en el dispositivo conectado y lo inicia. Debería ver "Hello World!" (Hola mundo) en la aplicación que se ejecuta en el dispositivo. Haga clic en **Run**->**Stop 'app'** (Ejecutar > Detener aplicación).
![Android Studio: Ejecutar](../../../includes/media/spatial-anchors-androidstudio/androidstudio-run.png)


## <a name="integrating-_arcore_"></a>Integración de _ARCore_

<a href="https://developers.google.com/ar/discover/" target="_blank">_ARCore_</a> es la plataforma de Google para la creación de experiencias de realidad aumentada, la habilitación del dispositivo para realizar un seguimiento de su posición a medida que se mueve y la creación de su propio conocimiento del mundo real.

Modifique `app\manifests\AndroidManifest.xml` para incluir las siguientes entradas en el nodo `<manifest>` raíz. Este fragmento de código realiza varias labores:

- Permitirá que la aplicación acceda a la cámara del dispositivo.
- También se garantizará que la aplicación solo podrán verla en Google Play Store los dispositivos que admiten ARCore.
- Se configurará Google Play Store para descargar e instalar ARCore, en caso de que no esté aún instalado, cuando se instale la aplicación.

```xml
<manifest ...>

    <uses-permission android:name="android.permission.CAMERA" />
    <uses-feature android:name="android.hardware.camera.ar" />

    <application>
        ...
        <meta-data android:name="com.google.ar.core" android:value="required" />
        ...
    </application>

</manifest>
```

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir la siguiente entrada. Este código garantiza que la aplicación tiene como destino la versión 1.25 de ARCore. Después de este cambio, es posible que reciba una notificación de Gradle en la que se le pida que realice la sincronización: haga clic en **Sync now** (Sincronizar ahora).

```gradle
dependencies {
    ...
    implementation 'com.google.ar:core:1.25.0'
    ...
}
```

## <a name="integrating-_sceneform_"></a>Integración de _Sceneform_

[_Sceneform_](https://developers.google.com/sceneform/develop/) simplifica la representación de escenas 3D realistas en aplicaciones de realidad aumentada sin tener que aprender OpenGL.

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir las siguientes entradas. Este código permitirá que la aplicación use construcciones del lenguaje de Java 8, lo que requiere `Sceneform`. También se asegurará de que la aplicación tiene como destino la versión 1.15 de `Sceneform`. Después de este cambio, es posible que reciba una notificación de Gradle en la que se le pida que realice la sincronización: haga clic en **Sync now** (Sincronizar ahora).

```gradle
android {
    ...

    compileOptions {
        sourceCompatibility JavaVersion.VERSION_1_8
        targetCompatibility JavaVersion.VERSION_1_8
    }
}

dependencies {
    ...
    implementation 'com.google.ar.sceneform.ux:sceneform-ux:1.15.0'
    ...
}
```

Abra el archivo `app\res\layout\activity_main.xml`y reemplace el elemento `<TextView ... />` de Hello World existente por el siguiente ArFragment. Este código hará que se muestre en pantalla la fuente de la cámara, lo que permite que ARCore realice un seguimiento de la posición del dispositivo mientras se mueve.


```xml
<fragment android:name="com.google.ar.sceneform.ux.ArFragment"
    android:id="@+id/ux_fragment"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

> [!NOTE]
> Para ver el xml sin formato de la actividad principal, haga clic en el botón "Código" o "Dividir" en la parte superior derecha de Android Studio.

[Vuelva a implementar](#trying-it-out) la aplicación en el dispositivo para validarla una vez más. Esta vez, se le deberían solicitar los permisos de la cámara. Tras la aprobación debería ver que la fuente de la cámara se representa en la pantalla.

## <a name="place-an-object-in-the-real-world"></a>Colocación de un objeto en el mundo real

Vamos a crear y colocar un objeto mediante la aplicación. En primer lugar, agregue las importaciones siguientes a `app\java\<PackageName>\MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=21-23,27-33,17-18)]

Luego, agregue las siguientes variables de miembro a la clase `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=47-52)]

Después, agregue el código siguiente al método `onCreate()` de `app\java\<PackageName>\MainActivity`. Este código enlazará un cliente de escucha, llamado `handleTap()`, que se detectará cuando el usuario toque la pantalla del dispositivo. Si da la casualidad de que el toque se produce en una superficie del mundo real que ya se ha reconocido en el seguimiento de ARCore, se ejecutará el cliente de escucha.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=63-69,80&highlight=6-7)]

Por último, agregue el siguiente método `handleTap()`, que agrupará todo. Creará una esfera y la colocará en la ubicación tocada. Esta esfera inicialmente será negra, ya que `this.recommendedSessionProgress` está establecido en cero en este momento. Este valor se ajustará más adelante.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-180,183-192,209)]

[Vuelva a implementar](#trying-it-out) la aplicación en el dispositivo para validarla una vez más. Esta vez, puede desplazar el dispositivo para que ARCore empiece a reconocer su entorno. Luego, toque la pantalla para crear la esfera negra y colocarla en la superficie que haya elegido.

## <a name="attach-a-local-azure-spatial-anchor"></a>Conexión de un anclaje espacial local de Azure

Modifique `Gradle Scripts\build.gradle (Module: app)` para incluir la siguiente entrada. Este fragmento de código de ejemplo tiene como destino la versión 2.10.2 del SDK de Azure Spatial Anchors. Tenga en cuenta que la versión 2.7.0 del SDK es la versión mínima admitida y que la referencia a cualquier versión más reciente de Azure Spatial Anchors también debería funcionar. Se recomienda usar la versión más reciente del SDK de Azure Spatial Anchors. [Aquí](https://github.com/Azure/azure-spatial-anchors-samples/releases) puede encontrar las notas de la versión del SDK.

```gradle
dependencies {
    ...
    implementation 'com.microsoft.azure.spatialanchors:spatialanchors_jni:[2.10.2]'
    implementation 'com.microsoft.azure.spatialanchors:spatialanchors_java:[2.10.2]'
    ...
}
```

Si el destino es el SDK 2.10.0 o posterior de Azure Spatial Anchors, incluya la siguiente entrada en la sección de repositorios del archivo `settings.gradle` del proyecto. Esto incluirá la dirección URL de la fuente de paquetes de Maven en la que se hospedan los paquetes Android de Azure Spatial Anchors para el SDK 2.10.0 o posterior: 

```gradle
dependencyResolutionManagement {
    ...
    repositories {
        ...
        maven {
            url 'https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Maven-packages/maven/v1'
        }
        ...
    }
}
```

Haga clic con el botón derecho en `app\java\<PackageName>`->**New**->**Java Class** (Nuevo > Clase Java). Establezca el **nombre** en _MyFirstApp_ y seleccione la **clase**. Se creará un archivo llamado `MyFirstApp.java`. Agréguele el siguiente elemento importado:

```java
import com.microsoft.CloudServices;
```

Defina `android.app.Application` como su superclase.
```java
public class MyFirstApp extends android.app.Application {...
```

Luego, agregue el siguiente código a la nueva clase `MyFirstApp`, lo que garantizará que Azure Spatial Anchors se inicializa con el contexto de la aplicación.

```java
    @Override
    public void onCreate() {
        super.onCreate();
        CloudServices.initialize(this);
    }
```

Ahora, modifique `app\manifests\AndroidManifest.xml` para incluir la siguiente entrada en el nodo `<application>` raíz. Este código enlazará la clase Application que creó en la aplicación.

```xml
    <application
        android:name=".MyFirstApp"
        ...
    </application>
```

En `app\java\<PackageName>\MainActivity`, agregue los siguientes elementos importados a:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=17,16,18,24,26,35,36,38&highlight=2-8)]

Luego, agregue las siguientes variables de miembro a la clase `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=52-56&highlight=3-5)]

A continuación, vamos a agregar el siguiente método `initializeSession()` a la clase `mainActivity`. Una vez que se le llama, garantiza que se crea una sesión de Azure Spatial Anchors y que se inicializa correctamente durante el inicio de la aplicación. Este código se asegura de que la sesión de sceneView pasada a la sesión de ASA a través de la llamada a `cloudSession.setSession` no sea NULL al tener una devolución anticipada.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=92-107,155)]

Puesto que `initializeSession()` podría realizar una devolución anticipada si la sesión de sceneView aún no está configurada (es decir, si `sceneView.getSession()` es NULL), agregamos una llamada onUpdate para asegurarnos de que la sesión ASA se inicializa una vez creada la sesión de sceneView.
[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=scene_OnUpdate)]

Ahora, vamos a enlazar el método `initializeSession()` y `scene_OnUpdate(...)` a su método `onCreate()`. Además, nos aseguraremos de que los fotogramas de la fuente de la cámara se envían al SDK de Azure Spatial Anchors para su procesamiento.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=63-80&highlight=9-17)]

Por último, agregue el código siguiente al método `handleTap()`. Conectará un anclaje espacial local de Azure a la esfera negra que vamos a colocar en el mundo real.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-192,209&highlight=12-13)]

[Vuelva a implementar](#trying-it-out) la aplicación. Desplace el dispositivo, toque la pantalla y coloque una esfera negra. Sin embargo, esta vez se creará el código y se conectará un anclaje espacial local de Azure a la esfera.

Antes de continuar,tiene que crear una cuenta de Azure Spatial Anchors, para obtener el identificador, la clave y el dominio de la cuenta, si no los tiene aún. En la sección siguiente se explica cómo obtenerlos.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Carga del anclaje local en la nube

Una vez que tenga el identificador, la clave y el domino de la cuenta de Azure Spatial Anchors, podemos volver a `app\java\<PackageName>\MainActivity` y agregarle los siguientes elementos importados:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=38-43&highlight=3-6)]

Luego, agregue las siguientes variables de miembro a la clase `MainActivity`:

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=56-61&highlight=3-6)]

Ahora, agregue el código siguiente al método `initializeSession()`. En primer lugar, este código permitirá que la aplicación supervise el progreso que hace el SDK de Azure Spatial Anchors cuando recopila los fotogramas de la fuente de la cámara. A medida que lo haga, el color de la esfera empezará a pasar del negro original a un color gris. Luego, pasará a ser blanco una vez que se hayan recopilado suficientes fotogramas para enviar el anclaje a la nube. En segundo lugar, este código proporcionará las credenciales necesarias para comunicarse con el back-end de nube. Aquí es donde se configurará la aplicación para que utilice el identificador, la clave y el dominio de la cuenta. Los copió en un editor cuando [configuró el recurso de Spatial Anchors](#create-a-spatial-anchors-resource).

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=92-130,151-155&highlight=17-43)]

A continuación, agregue el siguiente método `uploadCloudAnchorAsync()` a la clase `mainActivity`. Una vez que se le llame, este método esperará de forma asincrónica hasta que se recopilen suficientes fotogramas del dispositivo. En cuanto eso suceda, el color de la esfera cambiará a amarillo y, después, comenzará la carga del anclaje espacial local de Azure en la nube. Una vez finalizada la carga, el código devolverá un identificador del anclaje.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=uploadCloudAnchorAsync)]

Por último, vamos a enlazar todos los elementos. En el método `handleTap()`, agregue la siguiente línea. Esta línea invocará el método `uploadCloudAnchorAsync()` en cuanto se cree la esfera. Una vez que el método realice la devolución, el código siguiente realizará una actualización final a la esfera y su color cambiará a azul.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=159-167,179-209&highlight=26-39)]

[Vuelva a implementar](#trying-it-out) la aplicación. Desplace el dispositivo, toque la pantalla y coloque su esfera. No obstante, esta vez el color de la esfera cambiará de negro a blanco a medida que se recopilan los fotogramas de la cámara. Una vez que tengamos suficientes fotogramas, la esfera se pondrá amarilla y se iniciará la carga en la nube. Asegúrese de que el teléfono está conectado a Internet. Cuando finalice la carga, la esfera pasará a ser azul. Si lo desea, también puede supervisar la ventana `Logcat` en Android Studio para ver los mensajes de registro que envía la aplicación. Algunos ejemplos de mensajes que se registrarían son el progreso de la sesión durante la captura de fotogramas y el identificador de delimitador que devuelve la nube una vez completada la carga.

> [!NOTE]
> Si no ve cambiar el valor de `recommendedSessionProgress` (en los registros de depuración denominados `Session progress`), asegúrese de que **mueve y gira** el teléfono alrededor de la esfera que ha colocado.


## <a name="locate-your-cloud-spatial-anchor"></a>Búsqueda del anclaje espacial de la nube

Una vez que el anclaje se carga en la nube, ya es posible volver a intentar localizarlo. En primer lugar, vamos a agregar los elementos importados al código.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?range=43,44,34,37&highlight=3-4)]

Luego, vamos a agregar el siguiente código al método `handleTap()`. Este código hará lo siguiente:

- Quitar la esfera azul existente de la pantalla.
- Volver a inicializar la sesión de Azure Spatial Anchors. Esta acción garantizará que el anclaje que vamos a buscar proviene de la nube, en lugar del anclaje local que hemos creado.
- Emita una consulta para el anclaje que se ha cargado en la nube.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=handleTap&highlight=10-19)]

Ahora, vamos a enlazar el código que se invocará cuando se busca el anclaje que se consulta. En el método `initializeSession()`, agregue el siguiente código. Este fragmento de código creará una esfera verde y la colocará una vez que se encuentre el anclaje espacial de la nube. También volverá a habilitar la opción de tocar la pantalla, con el fin de que pueda repetir el escenario completo una vez más: crear otro anclaje local, cargarlo y volver a buscarlo.

[!code-java[MainActivity](../../../includes/spatial-anchors-new-android-app-finished.md?name=initializeSession&highlight=40-59)]

Eso es todo. [Vuelva a implementar](#trying-it-out) la aplicación una última vez para probar el escenario completo. Desplace el dispositivo y coloque la esfera negra. Luego, siga moviendo el dispositivo para capturar los fotogramas de la fuente de la cámara hasta que la esfera pase a ser amarilla. Se cargará el anclaje local y la esfera volverá a ser azul. Por último, toque la pantalla una vez más, con el fin de quitar el anclaje local y, después, consultaremos su homólogo en la nube. Siga desplazando el dispositivo hasta que se encuentre el anclaje espacial en la nube. Debe aparecer una esfera verde en la ubicación correcta, y puede volver a aclarar y repetir todo el escenario.

## <a name="putting-everything-together"></a>Todo junto

Así es como debería verse el archivo de clase `MainActivity` completo después de unir los distintos elementos. Puede usarlo como referencia para compararlo con su propio archivo, con el fin de detectar si es posible que queden diferencias.

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-new-android-app-finished.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial ha visto cómo crear una aplicación Android que integra la funcionalidad ARCore con Azure Spatial Anchors. Para más información acerca de la biblioteca de Azure Spatial Anchors, consulte nuestra guía sobre cómo crear y localizar los delimitadores.

> [!div class="nextstepaction"]
> [Creación y localización de delimitadores mediante Azure Spatial Anchors](../../../articles/spatial-anchors/create-locate-anchors-overview.md)