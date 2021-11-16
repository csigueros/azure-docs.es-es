---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: af26bc94e96c9d4bd1fdf59d5479bdc7181221f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298530"
---
Con el Administrador de recursos de Azure, se definen los parámetros de los valores que desea especificar al implementar la plantilla. La plantilla incluye una sección denominada Parámetros que contiene todos los valores de los parámetros. Debe definir un parámetro para esos valores que variarán según el proyecto que vaya a implementar o el entorno en el que vaya a realizar la implementación. No defina parámetros para los valores que son constantes. Cada valor de parámetro se usa en la plantilla para definir los recursos que se implementan.

Al definir parámetros, use el campo **allowedValues** para especificar los valores que un usuario puede proporcionar durante la implementación. Use el campo **defaultValue** para asignar un valor al parámetro, si no se proporciona ningún valor durante la implementación.

Vamos a describir cada parámetro de la plantilla.

### <a name="sitename"></a>siteName

El nombre de la aplicación web que desea crear.

```config
"siteName":{
  "type":"string"
}
```

### <a name="hostingplanname"></a>hostingPlanName

El nombre del plan de App Service que se va a crear para hospedar la aplicación web.

```config
"hostingPlanName":{
  "type":"string"
}
```

### <a name="sku"></a>sku

El nivel de precios del plan de hospedaje.

```json
"sku": {
  "type": "string",
  "allowedValues": [
    "F1",
    "D1",
    "B1",
    "B2",
    "B3",
    "S1",
    "S2",
    "S3",
    "P1",
    "P2",
    "P3",
    "P4"
  ],
  "defaultValue": "S1",
  "metadata": {
    "description": "The pricing tier for the hosting plan."
  }
}
```

La plantilla define los valores que se permiten para este parámetro y asigna un valor predeterminado de `S1` si no se especifica ningún valor.

### <a name="workersize"></a>workerSize

El tamaño de la instancia del plan de hospedaje (pequeño, mediano o grande).

```json
"workerSize":{
  "type":"string",
  "allowedValues":[
    "0",
    "1",
    "2"
  ],
  "defaultValue":"0"
}
```

La plantilla define los valores que se permiten para este parámetro (`0`, `1`, o `2`) y asigna un valor predeterminado de `0` si no se especifica ningún valor. Los valores corresponden al tamaño pequeño, mediano y grande.
