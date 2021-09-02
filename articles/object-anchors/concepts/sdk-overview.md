---
title: Introducción al SDK del entorno de ejecución
description: Familiarícese con el SDK del entorno de ejecución de Object Anchors.
author: craigktreasure
manager: rgarcia
services: azure-object-anchors
ms.author: crtreasu
ms.date: 07/23/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: bc197af9be61a98be1cb22c7ff9fd0e97c31fcbe
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/27/2021
ms.locfileid: "114710739"
---
# <a name="runtime-sdk-overview"></a>Introducción al SDK del entorno de ejecución

En esta sección se proporciona información general sobre el SDK del entorno de ejecución de Object Anchors, que se usa para detectar objetos mediante un modelo de Object Anchors. Conocerá cómo se representa un objeto y para qué se usan los distintos componentes.

Todos los tipos descritos a continuación se pueden encontrar en uno de los siguientes espacios de nombres: **Microsoft.Azure.ObjectAnchors**, **Microsoft.Azure.ObjectAnchors.Diagnostics** y **Microsoft.Azure.ObjectAnchors.SpatialGraph**.

## <a name="types"></a>Tipos

### <a name="objectmodel"></a>ObjectModel

Una clase [ObjectModel](/dotnet/api/microsoft.azure.objectanchors.objectmodel) representa la geometría de un objeto físico y codifica los parámetros necesarios para la detección y la estimación de la posición. Debe crearse mediante el [servicio Object Anchors](../quickstarts/get-started-model-conversion.md). Después, una aplicación puede cargar el archivo de modelo generado mediante la API de Object Anchors y consultar la malla insertada en ese modelo para su visualización.

### <a name="objectsearcharea"></a>ObjectSearchArea

Una clase [ObjectSearchArea](/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) especifica el espacio para buscar uno o varios objetos. Se define mediante un identificador de nodo de grafo espacial y límites espaciales en el sistema de coordenadas representado por el identificador de nodo del grafo espacial. El SDK del entorno de ejecución de Object Anchors admite cuatro tipos de límites, a saber, el **campo de visión**, el **rectángulo delimitador**, la **esfera** y una **ubicación**.

### <a name="accountinformation"></a>AccountInformation

[AccountInformation](/dotnet/api/microsoft.azure.objectanchors.accountinformation) almacena el identificador, la clave y el dominio de la cuenta de Azure Object Anchors.

### <a name="objectanchorssession"></a>ObjectAnchorsSession

[ObjectAnchorsSession](/dotnet/api/microsoft.azure.objectanchors.objectanchorssession) representa una sesión de Azure Object Anchors que se usa para crear instancias de ObjectObserver usadas para detectar objetos en el mundo físico.

### <a name="objectobserver"></a>ObjectObserver

Una clase [ObjectObserver](/dotnet/api/microsoft.azure.objectanchors.objectobserver) carga modelos de objetos, detecta sus instancias e informa de las posiciones 6-DoF (seis grados de libertad) de cada instancia en el sistema de coordenadas de HoloLens.

Aunque cualquier modelo de objetos o instancia se crea a partir de un **observador**, su duración es independiente. Una aplicación puede desechar un observador y seguir usando el modelo de objetos o la instancia.

### <a name="objectquery"></a>ObjectQuery

Una clase [ObjectQuery](/dotnet/api/microsoft.azure.objectanchors.objectquery) indica a un **observador de objetos** cómo buscar objetos de un modelo determinado. Proporciona los siguientes parámetros ajustables, cuyos valores predeterminados se pueden recuperar de un modelo de objetos.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

La propiedad [MinSurfaceCoverage](/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) indica el valor para considerar como detectada una instancia.

Para cada candidato de objeto, un **observador** calcula la relación entre las superficies superpuestas entre el modelo de objetos transformado y la escena y, a continuación, informa del candidato a la aplicación solo cuando la relación de cobertura está por encima de un umbral determinado.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

La propiedad [IsExpectedToBeStandingOnGroundPlane](/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) indica si se espera que el objeto de destino esté parado en el plano del suelo.

Un plano del suelo es el piso horizontal inferior del área de búsqueda. Proporciona una buena restricción para las posibles posiciones del objeto. Al activar esta marca, se guiará al **observador** para que estime la posición en un espacio limitado y pueda mejorar la precisión. Este parámetro se omitirá si no se supone que el modelo se encuentre en el plano del suelo.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

La propiedad [ExpectedMaxVerticalOrientationInDegrees](/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) indica el ángulo máximo esperado en grados entre la dirección ascendente de la instancia de un objeto y la gravedad.

Este parámetro proporciona otra restricción en la dirección ascendente de una posición estimada. Por ejemplo, si un objeto está de pie, este parámetro puede ser 0. Object Anchors no está diseñado para detectar objetos distintos del modelo. Si un modelo está de pie, no detectará una instancia donde esté acostado. Se usaría un nuevo modelo para el diseño acostado.
La misma regla se aplica para la articulación.

#### <a name="maxscalechange"></a>MaxScaleChange

La propiedad [MaxScaleChange](/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) indica el cambio máximo de escala de los objetos (dentro de 0 ~ 1) con respecto a la asignación espacial. La escala estimada se aplica a los vértices del objeto transformado, centrados en el origen y alineados en el eje. Es posible que las escalas estimadas no sean la escala real entre un modelo CAD y su representación física, sino algunos valores que permiten a la aplicación representar un modelo de objetos cerca de la asignación espacial en el objeto físico.

#### <a name="searchareas"></a>SearchAreas

La propiedad [SearchAreas](/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) indica una matriz de límites espaciales donde buscar objetos.

El **observador** buscará objetos en el espacio de unión de todas las áreas de búsqueda especificadas en una consulta. En esta versión, para reducir la latencia se devolverá como máximo un objeto con la mayor confianza.

### <a name="objectinstance"></a>ObjectInstance

Una clase [ObjectInstance](/dotnet/api/microsoft.azure.objectanchors.objectinstance) representa una posición hipotética en la que una instancia de un modelo dado podría estar en el sistema de coordenadas de HoloLens. Cada instancia incluye una propiedad `SurfaceCoverage` para indicar el grado de exactitud de la posición estimada.

Una instancia de se crea llamando al método `ObjectObserver.DetectAsync` y, a continuación, se actualiza automáticamente en segundo plano cuando está activa. Una aplicación puede escuchar el evento de cambio de estado en una instancia determinada o cambiar el modo de seguimiento para pausar o reanudar la actualización. Una instancia se quitará automáticamente del **observador** cuando se pierda el seguimiento.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

La clase [ObjectDiagnosticSession](/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) registra los diagnósticos y escribe datos en un archivo.

Un archivo de diagnóstico incluye la nube de puntos de una escena, el estado del observador e información sobre los modelos. Esta información es útil para identificar posibles problemas del entorno en tiempo de ejecución. Para más información, consulte las [preguntas más frecuentes](../faq.md).

## <a name="runtime-sdk-usage-and-details"></a>Uso y detalles del SDK del entorno de ejecución

En esta sección se brindan los conceptos básicos sobre cómo usar el SDK del entorno de ejecución. Proporciona un contexto suficiente para examinar las aplicaciones de ejemplo para ver cómo se usa Object Anchors de manera holística.

### <a name="initialization"></a>Inicialización

Las aplicaciones deben llamar a la API `ObjectObserver.IsSupported()` para determinar si se admite Object Anchors en el dispositivo antes de usarlo. Si la API `ObjectObserver.IsSupported()` devuelve `false`, compruebe que la aplicación tenga habilitada la funcionalidad **spatialPerception** o actualice al sistema operativo de HoloLens más reciente.

```cs
using Microsoft.Azure.ObjectAnchors;

if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
ObjectObserverAccessStatus status = await ObjectObserver.RequestAccessAsync();
if (status != ObjectObserverAccessStatus.Allowed)
{
    // Handle the error
}
```

A continuación, la aplicación crea un observador de objetos y carga los modelos necesarios generados por el [servicio de conversión del modelo de Object Anchors](../quickstarts/get-started-model-conversion.md).

```cs
using Microsoft.Azure.ObjectAnchors;

// Note that you need to provide the Id, Key and Domain for your Azure Object
// Anchors account.
Guid accountId = new Guid("[your account id]");
string accountKey = "[your account key]";
string accountDomain = "[your account domain]";

AccountInformation accountInformation = new AccountInformation(accountId, accountKey, accountDomain);
ObjectAnchorsSession session = new ObjectAnchorsSession(accountInformation);
ObjectObserver observer = session.CreateObjectObserver();

// Load a model into a byte array. The model could be a file, an embedded
// resource, or a network stream.
byte[] modelAsBytes;
ObjectModel model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed
// into a centered coordinate system for the ease of computing the object pose.
// The rigid transform can be retrieved through the `OriginToCenterTransform`
// property.
```

La aplicación crea una consulta para detectar instancias de ese modelo dentro de un espacio.

```cs
using Microsoft.Azure.ObjectAnchors;
using Microsoft.Azure.ObjectAnchors.SpatialGraph;
using Microsoft.Azure.ObjectAnchors.Unity;
using UnityEngine;

// Get the coordinate system.
SpatialGraphCoordinateSystem? coordinateSystem = null;

#if WINDOWS_UWP
SpatialCoordinateSystem worldOrigin = ObjectAnchorsWorldManager.WorldOrigin;
if (worldOrigin != null)
{
    coordinateSystem = await Task.Run(() => worldOrigin.TryToSpatialGraph());
}
#endif

if (!coordinateSystem.HasValue)
{
    Debug.LogError("no coordinate system?");
    return;
}

// Get the search area.
SpatialFieldOfView fieldOfView = new SpatialFieldOfView
{
    Position = Camera.main.transform.position.ToSystem(),
    Orientation = Camera.main.transform.rotation.ToSystem(),
    FarDistance = 4.0f, // Far distance in meters of object search frustum.
    HorizontalFieldOfViewInDegrees = 75.0f, // Horizontal field of view in
                                            // degrees of object search frustum.
    AspectRatio = 1.0f // Aspect ratio (horizontal / vertical) of object search
                       // frustum.
};

ObjectSearchArea searchArea = ObjectSearchArea.FromFieldOfView(coordinateSystem.Value, fieldOfView);

// Optionally change the parameters, otherwise use the default values embedded
// in the model.
ObjectQuery query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchArea);

// Detection could take a while, so we run it in a background thread.
IList<ObjectInstance> detectedObjects = await observer.DetectAsync(query);
```

De manera predeterminada, el **observador** hará un seguimiento automático de cada instancia detectada. Como opción, se pueden manipular estas instancias cambiando su modo de seguimiento o escuchando su evento de cambio de estado.

```cs
using Microsoft.Azure.ObjectAnchors;

foreach (ObjectInstance instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition"    - Consumes less CPU cycles thus fast to
    //                                 update the state.
    // "HighLatencyAccuratePosition" - (Not yet implemented) Consumes more CPU
    //                                 cycles thus potentially taking longer
    //                                 time to update the state.
    // "Paused"                      - Stops to update the state until mode
    //                                 changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    // instance.Dispose();
}
```

En el evento de cambio de estado, se puede consultar el estado más reciente o eliminar una instancia si perdió el seguimiento.

```cs
using Microsoft.Azure.ObjectAnchors;

var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    ObjectInstanceState? state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in
        // the same order to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance
        // and dispose it.
        sender.Dispose();
    }
});
```

Además, una aplicación tiene la opción de registrar una o varias sesiones de diagnóstico para la depuración sin conexión.

```cs
using Microsoft.Azure.ObjectAnchors;
using Microsoft.Azure.ObjectAnchors.Diagnostics;

string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
ObjectDiagnosticsSession diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance
// acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Por último, se liberan recursos mediante la eliminación de todos los objetos.

```cs
using Microsoft.Azure.ObjectAnchors;

foreach (ObjectInstance instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```