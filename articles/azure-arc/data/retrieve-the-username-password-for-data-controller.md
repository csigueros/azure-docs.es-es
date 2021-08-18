---
title: Recuperación del nombre de usuario y la contraseña para conectarse al controlador de datos de Arc
description: Recuperación del nombre de usuario y la contraseña para conectarse al controlador de datos de Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 89ba0139409a1ceef37a773403c0b7623f6c6f14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738598"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>Recuperación del nombre de usuario y la contraseña para conectarse al controlador de datos de Arc

Es posible que se produzca una situación en la que sea necesario recuperar el nombre de usuario y la contraseña del controlador de datos. Estos son los comandos que necesitará cuando lo ejecute. 

Si es el administrador de Kubernetes del clúster. Como tal, cuenta con privilegios para ejecutar comandos con el fin de recuperar de Kubernetes el secreto que almacena la información que Azure Arc conserva allí.

> [!NOTE]
>  Si ha usado un nombre diferente para el espacio de nombres en el que se ha creado el controlador de datos, asegúrese de cambiar el parámetro `-n arc` en los comandos siguientes a fin de usar el nombre del espacio de nombres en el que ha creado el controlador de datos.


## <a name="linux"></a>Linux

Ejecute el comando siguiente para recuperar el nombre de usuario:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

Ejecute el comando siguiente para recuperar la contraseña:

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

Ejecute el comando siguiente para recuperar el nombre de usuario:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

Ejecute el comando siguiente para recuperar la contraseña:

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>Pasos siguientes

Pruebe otros [escenarios](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md).
