---
title: Descripción del manifiesto APT de Device Update para Azure IoT Hub | Microsoft Docs
description: Descubra cómo Device Update para IoT Hub usa el manifiesto APT para una actualización basada en paquetes.
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 24c0d04b6a29b813dc23604594ca4b1b1235dc37
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373220"
---
# <a name="device-update-apt-manifest"></a>Manifiesto APT de Device Update

El manifiesto APT es un archivo JSON que describe los detalles de una actualización que necesita el controlador APT Update. Este archivo se puede importar en Device Update para IoT Hub igual que cualquier otra actualización.

[Obtenga más información](import-update.md) sobre la importación de actualizaciones en Device Update.

## <a name="overview"></a>Información general

Cuando un manifiesto APT se entrega a un agente de Device Update como una actualización, el agente debe procesar el manifiesto y llevar a cabo las operaciones necesarias. Estas operaciones incluyen la descarga y la instalación de los paquetes especificados en el archivo de manifiesto APT y sus dependencias desde un repositorio designado.

Device Update admite APT UpdateType y APT Update Handler. Esta compatibilidad permite al agente de Device Update evaluar los paquetes de Debian instalados y actualizar los paquetes necesarios. 

## <a name="schema"></a>Schema

Un archivo de manifiesto APT es un archivo JSON que incluye un esquema con versiones.

```json
{
    "name": "<name>",
    "version": "<version>",
    "packages": [
        {
            "name": "<package name>",
            "version": "<version specifier>"
        }
    ]
}
```

Ejemplo:

```json
{
    "name": "contoso-iot-edge",
    "version": "1.0.0.0",
    "packages": [
        {
            "name" : "thermocontrol",
            "version" : "1.0.1"
        },
        {
            "name" : "tempreport",
            "version" : "2.0.0"
        }
    ]
}
```

### <a name="name"></a>name

Nombre de este manifiesto APT. Puede ser cualquier nombre o identificador significativo para sus escenarios. Por ejemplo, `contoso-iot-edge`.

### <a name="version"></a>version

Número de versión de este manifiesto APT. Por ejemplo, `1.0.0.0`.


### <a name="packages"></a>packages

Lista de objetos que contiene propiedades específicas de los paquetes.

#### <a name="name"></a>name

Nombre o identificador del paquete. Por ejemplo, `iotedge`.

#### <a name="version"></a>version

Criterios de versión deseados para el paquete. Por ejemplo, `1.0.8-2`.

Actualmente solo se admite el número de versión exacto. El número de versión es la versión de paquete de Debian deseada en formato [epoch:]upstream_version[-debian_revision].

**epoch** es un entero sin signo.

**upstream_version** pueden incluir caracteres alfanuméricos y caracteres como ".", "+", "-" y "~". Debe empezar por un dígito.
> [!NOTE]
> "1.0.8" es igual a "1.0.8-0".

Por ejemplo, **`"name":"iotedge" and "version":"1.0.8-2"`** es equivalente a instalar un paquete mediante el comando `apt-get install iotedge=1.0.8-2`.

> [!NOTE]
> El valor de versión no contiene el signo igual.

Si se omite la versión, se instalará la última versión disponible del paquete especificado.

[Obtenga más información](https://www.debian.org/doc/debian-policy/ch-controlfields.html#s-f-version) sobre cómo se controlan las versiones de los paquetes de Debian.

> [!NOTE]
> El administrador de paquetes APT omite los requisitos de control de versiones que indica un paquete cuando los paquetes dependientes que se van a instalar se resuelven automáticamente. A menos que se proporcionen versiones explícitas de los paquetes dependientes, se usará la más reciente, aunque el propio paquete pueda especificar un requisito estricto (=) en una versión determinada. Esta resolución automática puede provocar errores relacionados con una dependencia que no se ha cumplido. [Más información](https://unix.stackexchange.com/questions/350192/apt-get-not-properly-resolving-a-dependency-on-a-fixed-version-in-a-debian-ubunt)

Si está actualizando una versión específica del demonio de seguridad de Azure IoT Edge, debe incluir la versión deseada del paquete `iotedge` y su paquete `libiothsm-std` dependiente en el manifiesto APT.
[Más información](../iot-edge/how-to-update-iot-edge.md#update-the-security-daemon)

> [!NOTE]
> Se puede usar un manifiesto APT para actualizar el agente de Device Update y sus dependencias. Enumere el nombre del agente de Device Update y la versión deseada en el manifiesto APT, como lo haría con cualquier otro paquete. Este manifiesto APT se puede importar e implementar a través de la canalización de Device Update para IoT Hub. 

## <a name="removing-packages"></a>Eliminación de paquetes

También puede usar un manifiesto APT para quitar paquetes instalados del dispositivo. Se puede usar un único manifiesto APT para quitar, agregar y actualizar varios paquetes. Para quitar un paquete, agregue un signo menos "-" después del nombre del paquete. No debe incluir ningún número de versión para los paquetes que va a quitar. La eliminación de paquetes a través de un manifiesto APT no quita sus dependencias ni configuraciones.

Ejemplo:

```json
{
    "name": "contoso-video",
    "version": "2.0.0.1",
    "packages": [
        {
            "name" : "foo-"
        }
    ]
}
```
Este manifiesto APT quitará el paquete "foo" de los dispositivos en los que está implementado. 

## <a name="recommended-value-for-installed-criteria"></a>Valor recomendado de Installed Criteria (Criterios instalados)

Installed Criteria (Criterios instalados) de un manifiesto APT es `<name>-<version>` donde `<name>` es el nombre del manifiesto APT y `<version>` es la versión del manifiesto APT. Por ejemplo, `contoso-iot-edge-1.0.0.0`. 

## <a name="guidelines-on-creating-an-apt-manifest"></a>Directrices para crear un manifiesto APT

Al crear el manifiesto APT, hay algunas instrucciones que se deben tener en cuenta:

- Asegúrese siempre de que el manifiesto APT sea un archivo JSON con el formato correcto.
- Cada manifiesto APT debe tener una versión única. Intente crear una metodología normalizada para incrementar la versión del manifiesto APT, de modo que tenga sentido para sus escenarios y se pueda seguir fácilmente.
- En cuanto al estado deseado de cada paquete individual, especifique el nombre exacto y la versión del paquete que desea instalar en el dispositivo. Valide siempre los valores en el repositorio de paquetes que desea utilizar como origen del paquete.
- Asegúrese de que los paquetes del manifiesto APT aparecen en el orden en que se deben instalar o quitar.
- Valide siempre la instalación de paquetes en un dispositivo de prueba para asegurarse de que el resultado es el previsto.
- Al instalar una versión específica de un paquete (por ejemplo, `iotedge 1.0.9-1`), se recomienda tener también en el manifiesto APT las versiones explícitas de los paquetes dependientes que se van a instalar (por ejemplo, `libiothsm 1.0.9-1`).
- Aunque no es obligatorio, asegúrese siempre de que el manifiesto APT sea acumulativo para evitar que el dispositivo tenga un estado desconocido. Una actualización acumulativa garantizará que los dispositivos tengan la versión deseada de cada paquete que le interese, aunque el dispositivo haya omitido una implementación de APT Update debido a un error en la instalación o se haya desconectado.

Por ejemplo:

**Manifiesto APT base**

```JSON
{
    "name": "contoso-iot-edge",
    "version": "1.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        }
    ]
}
```

**ACTUALIZACIÓN INCORRECTA**

Esta actualización incluye el paquete bar, pero no el paquete foo.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

**ACTUALIZACIÓN CORRECTA**

Esta actualización incluye el paquete foo y también el paquete bar.

```JSON
{
    "name": "contoso-iot-edge",
    "version": "2.0",
    "packages": [
        {
            "name": "foo",
            "version": "1.0.1"
        },
        {
            "name": "bar",
            "version": "3.0.2"
        }
    ]
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Importación de una nueva actualización](import-update.md)