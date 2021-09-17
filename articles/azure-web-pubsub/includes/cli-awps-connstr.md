---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: 4bfab858862bfb08cfaa2e9dc685b8ade7c0325e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785577"
---
> [!Important]
> Una cadena de conexión incluye la información de autorización necesaria para que la aplicación acceda al servicio Azure Web PubSub. La clave de acceso dentro de la cadena de conexión es similar a una contraseña raíz para el servicio. Siempre debe proteger las claves de acceso. Use Azure Key Vault para administrar y rotar las claves de forma segura. Evite distribuirlas a otros usuarios, codificarlas de forma rígida o guardarlas en un archivo de texto sin formato al que puedan acceder otros usuarios. Rote sus claves si cree que se han puesto en peligro.

Use el comando [az webpubsub key](/cli/azure/webpubsub#az_webpubsub_key) de la CLI de Azure para obtener el valor de **ConnectionString** del servicio.

```azurecli-interactive
az webpubsub key show --name "<your-unique-resource-name>" --resource-group "myResourceGroup" --query primaryConnectionString
```
