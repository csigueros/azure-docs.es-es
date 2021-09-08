---
title: 'Introducción a la fuente de cambios de DICOM: Azure Healthcare APIs'
description: En este artículo conocerá los conceptos de la fuente de cambios de DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: aersoy
ms.openlocfilehash: fc23c4cedbb6ee94c4f253cf53e0cdde27531fb4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780524"
---
# <a name="change-feed-overview"></a>Introducción a la fuente de cambios

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

La fuente de cambios proporciona registros de todos los cambios que se producen en el servicio DICOM. La fuente de cambios proporciona registros ordenados, garantizados, inmutables y de solo lectura de estos cambios. La fuente de cambios ofrece la posibilidad de revisar el historial del servicio DICOM y actuar con relación a los eventos de creación y eliminación que tienen lugar en este.

Las aplicaciones cliente pueden leer estos registros en cualquier momento, ya sea en streaming o en modo por lotes. La fuente de cambios le permite compilar soluciones eficaces y escalables que procesan los eventos de cambio que se producen en su servicio DICOM.

Puede procesar dichos eventos de manera asincrónica o incremental, o bien en su totalidad. Cualquier número de aplicaciones cliente puede leer de manera independiente la fuente de cambios, en paralelo y a su propio ritmo.

Asegúrese de especificar la versión como parte de la dirección URL al realizar solicitudes. Para obtener más información, consulte [Control de versiones de API para el servicio DICOM](api-versioning-dicom-service.md).

## <a name="api-design"></a>Diseño de API

La API expone dos puntos de conexión `GET` para interactuar con la fuente de cambios. [Más adelante se proporciona](#example-usage-flow) un flujo típico para consumir la fuente de cambios.

Verbo | Ruta              | Devoluciones     | Descripción
:--- | :----------------- | :---------- | :---
GET  | /changefeed        | Matriz JSON  | [Se lee la fuente de cambios.](#read-change-feed)
GET  | /changefeed/latest | Objeto JSON | [Se lee la entrada más reciente de la fuente de cambios.](#get-latest-change-feed-item)

### <a name="object-model"></a>Modelo de objetos

Campo               | Tipo      | Descripción
:------------------ | :-------- | :---
Secuencia            | int       | Id. de secuencia que se puede usar para paginación (mediante desplazamiento) o anclaje.
StudyInstanceUid    | string    | UID de instancia del estudio.
SeriesInstanceUid   | string    | UID de instancia de serie.
SopInstanceUid      | string    | UID de instancia de procedimientos normalizados de trabajo.
Acción              | string    | Acción que se ha realizado: `create` o `delete`.
Timestamp           | datetime  | Fecha y hora de la acción en UTC.
Estado               | string    | [Estado actual de los metadatos.](#states)
Metadatos            | object    | Opcionalmente, metadatos actuales de DICOM si la instancia existe.

#### <a name="states"></a>States

Estado    | Descripción
:------- | :---
actuales  | Esta instancia es la versión actual.
reemplazado | Esta instancia se ha reemplazado por una nueva versión.
deleted  | Esta instancia se ha eliminado y ya no está disponible en el servicio.

### <a name="read-change-feed"></a>Lectura de la fuente de cambios

**Ruta**: /changefeed?offset={int}&limit={int}&includemetadata={**true**|false}
```
[
    {
        "Sequence": 1,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-04T01:03:08.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    },
    {
        "Sequence": 2,
        "StudyInstanceUid": "{uid}",
        "SeriesInstanceUid": "{uid}",
        "SopInstanceUid": "{uid}",
        "Action": "create|delete",
        "Timestamp": "2020-03-05T07:13:16.4834Z",
        "State": "current|replaced|deleted",
        "Metadata": {
            "actual": "metadata"
        }
    }
    ...
]
```
#### <a name="parameters"></a>Parámetros

Nombre            | Tipo | Descripción
:-------------- | :--- | :---
offset          | int  | Número de registros que se omitirán antes de los valores que se devolverán.
limit           | int  | Número de registros que se devolverán (valor predeterminado: 10; mín.: 1; máx.: 100).
includemetadata | bool | Si se deben incluir o no los metadatos (valor predeterminado: "true").

### <a name="get-latest-change-feed-item"></a>Obtención del elemento más reciente de la fuente de cambios

**Ruta**: /changefeed/latest?includemetadata={**true**|false}

```
{
    "Sequence": 2,
    "StudyInstanceUid": "{uid}",
    "SeriesInstanceUid": "{uid}",
    "SopInstanceUid": "{uid}",
    "Action": "create|delete",
    "Timestamp": "2020-03-05T07:13:16.4834Z",
    "State": "current|replaced|deleted",
    "Metadata": {
        "actual": "metadata"
    }
}
```

#### <a name="parameters"></a>Parámetros

Nombre            | Tipo | Descripción
:-------------- | :--- | :---
includemetadata | bool | Si se deben incluir o no los metadatos (valor predeterminado: "true").

## <a name="usage"></a>Uso

### <a name="example-usage-flow"></a>Flujo de uso de ejemplo

A continuación se muestra el flujo de uso de una aplicación de ejemplo que realiza otro procesamiento en las instancias del servicio DICOM.

1. La aplicación que quiere supervisar la fuente de cambios inicia el flujo.
2. Determina si hay un estado actual por el que debe empezar:
   * Si tiene un estado, usa el desplazamiento (secuencia) almacenado.
   * Si no lo ha iniciado nunca y quiere empezar desde el principio, usa `offset=0`.  
   * Si solo quiere procesar a partir de ahora, envía una consulta a `/changefeed/latest` para obtener la última secuencia.
3. Consulta la fuente de cambios con el desplazamiento `/changefeed?offset={offset}` especificado.
4. Si hay entradas:
   * Realiza un procesamiento adicional.  
   * Actualiza su estado actual.  
   * Empieza de nuevo en el paso 2 anterior.
5. Si no hay ninguna entrada, se suspende durante un período de tiempo configurado y empieza de nuevo en el paso 2.

### <a name="other-potential-usage-patterns"></a>Otros patrones de uso potenciales

La compatibilidad con la fuente de cambios es adecuada para escenarios en los que se procesan datos en función de los objetos que han cambiado. Por ejemplo, se puede utilizar para lo siguiente:

* Crear canalizaciones de aplicaciones conectadas, como ML, que reaccionen a eventos de cambio o programen ejecuciones basadas en una instancia creada o eliminada.
* Extraer métricas e información de análisis de negocios, en función de los cambios que se produzcan en sus objetos.
* Sondear la fuente de cambios para crear un origen de evento y enviar notificaciones push.

## <a name="summary"></a>Resumen

En este artículo hemos revisado el diseño de la API de REST de la fuente de cambios y los posibles escenarios de uso. Para obtener información sobre la fuente de cambios, consulte [Incorporación de cambios de la fuente de cambios](pull-dicom-changes-from-change-feed.md).

## <a name="next-steps"></a>Pasos siguientes

>[!div class="nextstepaction"]
>[Introducción al servicio DICOM](dicom-services-overview.md)

