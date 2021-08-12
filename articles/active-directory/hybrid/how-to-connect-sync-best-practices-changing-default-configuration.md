---
title: 'Sincronización de Azure AD Connect: cambio de la configuración predeterminada | Microsoft Docs'
description: Proporciona procedimientos recomendados para cambiar la configuración predeterminada de Azure AD Connect Sync.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 7638a031-1635-4942-94c3-fce8f09eed5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/29/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: afe4db68b5a5d508aaf9760f4da0a8fd15890e15
ms.sourcegitcommit: 6323442dbe8effb3cbfc76ffdd6db417eab0cef7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110614870"
---
# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Azure AD Connect Sync: procedimientos recomendados de cambio de la configuración predeterminada
El propósito de este tema es describir los cambios admitidos y no admitidos en la sincronización de Azure AD Connect.

La configuración creada por Azure AD Connect funciona "tal cual" para la mayoría de entornos que sincronizan Active Directory local con Azure AD. Sin embargo, en algunos casos, es necesario aplicar algunos cambios a una configuración para satisfacer un requisito o una necesidad en particular.

## <a name="changes-to-the-service-account"></a>Cambios en la cuenta de servicio
Azure AD Connect Sync se está ejecutando con una cuenta de servicio creada por el asistente para la instalación. Esta cuenta de servicio contiene las claves de cifrado para la base de datos usada en la sincronización. Se crea con una contraseña de 127 caracteres, que se configura para que no caduque.

> [!WARNING]
> Si cambia o restablece la contraseña de la cuenta de servicio de ADSync, el servicio de sincronización no podrá iniciarse correctamente hasta que haya abandonado la clave de cifrado y reinicializado la contraseña de la cuenta de servicio de ADSync.
> Para ello, vea [Cambio de la contraseña de la cuenta de servicio de ADSync](how-to-connect-sync-change-serviceacct-pass.md).

## <a name="changes-to-the-scheduler"></a>Cambios realizados en el programador
A partir de las versiones de compilación 1.1 (febrero de 2016) puede configurar el [Programador](how-to-connect-sync-feature-scheduler.md) para que tenga un ciclo de sincronización diferente al del valor predeterminado de 30 minutos.

## <a name="changes-to-synchronization-rules"></a>Cambios en las reglas de sincronización
El Asistente para la instalación proporciona una configuración que se supone que funciona en la mayoría de los casos. En caso de que tenga que realizar cambios en la configuración, debe seguir entonces estas reglas para disponer de una configuración admitida.

> [!WARNING]
> Si realiza cambios en las reglas de sincronización predeterminadas, estos cambios se sobrescribirán la próxima vez que se actualice Azure AD Connect, lo que provocará resultados de sincronización inesperados y, probablemente, no deseados.

* Puede [cambiar los flujos de atributo](how-to-connect-sync-change-the-configuration.md#other-common-attribute-flow-changes) si los flujos de atributo directos predeterminados no son adecuados para su organización.
* Si no desea [pasar un atributo](how-to-connect-sync-change-the-configuration.md#do-not-flow-an-attribute) ni quitar valores de atributo existentes en Azure AD, deberá crear una regla para este escenario.
* [Deshabilite una regla de sincronización no deseada](#disable-an-unwanted-sync-rule) en lugar de eliminarla. Una regla eliminada se vuelve a crear durante una actualización.
* Para [cambiar una regla lista para usar](#change-an-out-of-box-rule), debe realizar una copia de la regla original y deshabilitar la regla lista para usar. Aparece el Editor de reglas de sincronización para brindarle ayuda.
* Exporte las reglas de sincronización personalizadas mediante el editor de reglas de sincronización. El editor le ofrece un script de PowerShell que puede utilizar para volver a crearlas fácilmente en un escenario de recuperación ante desastres.

> [!WARNING]
> Las reglas de sincronización listas para usar tienen una huella digital. Si realiza un cambio en estas reglas, la huella digital ya no coincidirá. Es posible que tenga problemas en el futuro cuando intente aplicar una nueva versión de Azure AD Connect. Realiza únicamente cambios cómo se describe en este artículo.

### <a name="disable-an-unwanted-sync-rule"></a>Deshabilite una regla de sincronización no deseada
No elimine una regla de sincronización lista para usar. Esta se vuelve a crear durante la siguiente actualización.

En algunos casos, el asistente de instalación ha generado una configuración que no funciona en su topología. Por ejemplo, si tiene una topología de bosque de cuentas-recursos pero ha ampliado el esquema en el bosque de cuentas con el esquema de Exchange, entonces se crean reglas para Exchange tanto para el bosque de cuentas como para el bosque de recursos. En este caso, tenemos que deshabilitar la regla de sincronización para Exchange.

![Regla de sincronización deshabilitada](./media/how-to-connect-sync-best-practices-changing-default-configuration/exchangedisabledrule.png)

En la imagen anterior, el Asistente para la instalación ha encontrado un esquema antiguo de Exchange 2003 en el bosque de cuentas. Este esquema de extensión se agregó antes de que el bosque de recursos se introdujera en el entorno de Fabrikam. Para asegurarse de que ningún atributo de la implementación antigua de Exchange se sincronice, la regla de sincronización se debe deshabilitar como se muestra.

### <a name="change-an-out-of-box-rule"></a>cambiar una regla lista para usar
La única vez que se debe cambiar una regla integrada es cuando tiene que cambiar la regla de unión. Si tiene que cambiar un flujo de atributo, debe crear una regla de sincronización con mayor prioridad que las reglas integradas. La única regla que necesita desde un punto de vista práctico es la regla **In from AD - User Join**. Puede invalidar el resto de reglas con una regla de prioridad superior.

Si tiene que realizar cambios en una regla lista para usar, debe realizar una copia de esta y deshabilitar la regla original. A continuación, realice los cambios en la regla clonada. El Editor de reglas de sincronización le ayuda con esos pasos. Cuando se abre una regla lista para usar, se presenta este cuadro de diálogo:   
![Regla lista para usar de advertencia](./media/how-to-connect-sync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Seleccione **Sí** para crear una copia de la regla. A continuación, se abre la regla clonada.  
![Regla clonada](./media/how-to-connect-sync-best-practices-changing-default-configuration/clonedrule.png)

En esta regla clonada, realice los cambios necesarios en el ámbito, la combinación y las transformaciones.

## <a name="next-steps"></a>Pasos siguientes
**Temas de introducción**

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](how-to-connect-sync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](whatis-hybrid-identity.md)
