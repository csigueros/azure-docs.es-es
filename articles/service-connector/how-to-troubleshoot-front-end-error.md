---
title: Guía de solución de problemas de Service Connector
description: Lista de errores y acciones sugeridas de Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8a1379495dacba7d2c0cf21af3a1e5ec2f45ed41
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283190"
---
# <a name="how-to-troubleshoot-with-service-connector"></a>Solución de problemas con Service Connector

Si se encuentra con un problema, puede consultar el mensaje de error para encontrar las acciones o las correcciones sugeridas. En esta guía paso a paso se muestran las opciones para solucionar problemas de Service Connector.

## <a name="error-message-and-suggested-actions-from-portal"></a>Mensaje de error y acciones sugeridas de Portal

| Mensaje de error | Acción sugerida |
| --- | --- |
| Tipo de recurso desconocido | <ul><li>Compruebe el origen y el recurso de destino para verificar si Service Connector admite los tipos de servicio.</li><li>Compruebe si Service Connector admite la combinación de conexión de origen y destino especificada.</li></ul> |
| Tipo de recurso desconocido | <ul><li>Compruebe si el recurso de destino existe.</li><li>Compruebe si el id. del recurso de destino es correcto.</li></ul> |
| Recurso no admitido | <ul><li>Compruebe si el tipo de autenticación es compatible con la combinación de conexión de origen y destino especificada.</li></ul> |

### <a name="error-typeerror-message-and-suggested-actions-using-azure-cli"></a>Tipo de error, mensaje de error y acciones sugeridas con CLI de Azure

### <a name="invalidargumentvalueerror"></a>InvalidArgumentValueError


| Mensaje de error | Acción sugerida |
| --- | --- |
| El id. del recurso de origen no es válido: `{SourceId}` | <ul><li>Compruebe si el id. del recurso de origen es compatible con Service Connector.</li><li>Compruebe si el id. del recurso de origen es correcto.</li></ul> |
| El id. del recurso de destino no es válido: `{TargetId}` | <ul><li>Compruebe si el tipo de servicio de destino es compatible con Service Connector.</li><li>Compruebe si el id. del recurso de destino es correcto.</li></ul> |
| El id. de conexión no es válido: `{ConnectionId}` | <ul><li>Compruebe si el id. de conexión es correcto.</li></ul> |


### <a name="requiredargumentmissingerror"></a>RequiredArgumentMissingError

| Mensaje de error | Acción sugerida |
| --- | --- |
| `{Argument}` no debe estar en blanco | El usuario debe proporcionar el valor del argumento para la entrada interactiva |
| Faltan claves necesarias para el parámetro `{Parameter}`. Todas las claves posibles son `{Keys}` | Proporcione el valor para el parámetro de información de autenticación, normalmente en forma de `--param key1=val1 key2=val2`. |
| Falta el argumento necesario; proporcione los argumentos: `{Arguments}` | Proporcione el argumento necesario. | 

### <a name="validationerror"></a>ValidationError

| Mensaje de error | Acción sugerida |
| --- | --- |
| Solo se necesita un parámetro de información de autenticación | El usuario solo puede proporcionar un parámetro de información de autenticación. Compruebe si no se proporciona ningún parámetro o si se proporcionan varios. |
| Se debe proporcionar el argumento de información de autenticación al actualizar la conexión: `{ConnectionName}` | Al actualizar una conexión de tipo secreto, se debe proporcionar el parámetro de información de autenticación. (Se debe a que no se puede acceder al secreto del usuario a través de la API de ARM) |
| Se debe especificar el tipo de cliente o la información de autenticación para actualizar | Al actualizar una conexión, se debe facilitar información de tipo de cliente o autenticación. |
| Error de uso: {} [KEY=VALUE ...] | Compruebe las claves disponibles y proporcione valores para el parámetro de información de autenticación, normalmente en forma de `--param key1=val1 key2=val2`. |
| La clave no admitida `{Key}` se proporciona para el parámetro `{Parameter}`. Todas las claves posibles son `{Keys}` | Compruebe las claves disponibles y proporcione valores para el parámetro de información de autenticación, normalmente en forma de `--param key1=val1 key2=val2`. |
| Error de aprovisionamiento. Cree manualmente el recurso de destino y, a continuación, la conexión. Detalles del error: `{ErrorTrace}` | <ul><li>Reintentar.</li><li>Cree manualmente el recurso de destino y, a continuación, la conexión.</li></ul> |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
