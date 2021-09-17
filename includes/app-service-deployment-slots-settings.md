---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 062b46350412f3aec2eb10deddaf9b4cab37a4a4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453694"
---
Cuando crea un clon de la configuración de otro espacio de implementación, la configuración clonada se puede editar. Algunos elementos de configuración siguen al contenido en los intercambios (no son específicos de la ranura), mientras que otros permanecen en la misma ranura después de este (específicos). Las listas siguientes muestran la configuración que cambia cuando se intercambian las ranuras.

**Configuraciones que se intercambian**:

* Configuración general: por ejemplo, versión de Framework, 32 o 64 bits, Web Sockets
* Configuración de la aplicación (puede configurarse para ajustarse a un espacio)
* Cadenas de conexión (puede configurarse para ajustarse a un espacio)
* Asignaciones de controlador
* Certificados públicos
* Contenido de WebJobs
* Conexiones híbridas *
* Puntos de conexión de servicio *
* Azure Content Delivery Network *
* Asignaciones de ruta de acceso

Se prevé que las características marcadas con un asterisco (*) no se intercambien. 

**Valores que no se intercambian**:

* Extremos de publicación
* Nombres de dominio personalizados
* Certificados no públicos y configuración de TLS/SSL
* Configuración de escala
* Programadores de WebJobs
* Restricciones de IP
* Always On
* Configuración de diagnóstico
* Uso compartido de recursos entre orígenes (CORS)
* Integración de la red virtual
* Identidades administradas
* Configuración que termina con el sufijo _EXTENSION_VERSION

> [!NOTE]
> Para que la configuración mencionada anteriormente sea intercambiable, agregue la configuración de la aplicación `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` en todas las ranuras de la aplicación y establezca su valor en `0` o `false`. Esta configuración se puede intercambiar o no. Recuerde que no puede hacer que solo algunos valores de configuración sean intercambiables y que los demás no lo sean. Las identidades administradas nunca se intercambian y no se ven afectadas por esta invalidación de la configuración de la aplicación

> Algunas configuraciones de aplicaciones que se aplican a configuraciones no intercambiadas no se intercambian. Por ejemplo, como los valores de diagnóstico no se intercambian, los valores de aplicaciones relacionados como `WEBSITE_HTTPLOGGING_RETENTION_DAYS` y `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` tampoco lo hacen, aunque no se muestren como valores de ranura.
>
