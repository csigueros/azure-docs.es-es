---
title: Versión preliminar del acceso condicional de Azure Active Directory para identidades de carga de trabajo
description: Protección de identidades de carga de trabajo con directivas de acceso condicional
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57af80a8baea02532d8711a57963668bf63ece81
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725472"
---
# <a name="conditional-access-for-workload-identities-preview"></a>Versión preliminar del acceso condicional para las identidades de carga de trabajo

Anteriormente, las directivas de acceso condicional solo se aplicaban a los usuarios cuando accedían a aplicaciones y servicios como SharePoint en línea o Azure Portal. Esta versión preliminar agrega compatibilidad con las directivas de acceso condicional aplicadas a las entidades de servicio que pertenecen a la organización. Esta funcionalidad se llama acceso condicional para identidades de carga de trabajo. 

Una identidad de carga de trabajo es una identidad que permite a una aplicación o entidad de servicio acceder a los recursos, a veces en el contexto de un usuario. Estas identidades de carga de trabajo difieren de las cuentas de usuario tradicionales ya que:

- Normalmente no tienen ningún proceso de ciclo de vida formal.
- Es necesario almacenar sus credenciales o secretos en algún lugar.
- Las aplicaciones pueden usar varias identidades. 
 
Estas diferencias hacen que las identidades de carga de trabajo sean difíciles de administrar, tengan un mayor riesgo de pérdida y menor capacidad para proteger el acceso.

> [!NOTE]
> La directiva se puede aplicar a entidades de servicio de un solo inquilino que se hayan registrado en el inquilino. Las aplicaciones SaaS y multiinquilino de terceros se encuentran fuera del ámbito. La directiva no abarca a las identidades administradas. 

Esta versión preliminar permite bloquear las entidades de servicio desde fuera de los intervalos IP de confianza como, por ejemplo, los intervalos IP públicos de una red corporativa. 

## <a name="implementation"></a>Implementación

### <a name="step-1-set-up-a-sample-application"></a>Paso 1: Configuración de una aplicación de ejemplo

Si ya tiene una aplicación de prueba que usa una entidad de servicio, puede omitir este paso.

Configure una aplicación de ejemplo que muestre cómo se puede ejecutar un trabajo o un servicio de Windows con una identidad de aplicación, en lugar de la identidad de un usuario. Siga las instrucciones del artículo [Inicio rápido: Adquisición de un token y llamada a Microsoft Graph API mediante una identidad de aplicación de consola](../develop/quickstart-v2-netcore-daemon.md) para crear esta aplicación.

### <a name="step-2-create-a-conditional-access-policy"></a>Paso 2: Creación de una directiva de acceso condicional

Cree una directiva de acceso condicional basada en la ubicación que se aplique a las entidades de servicio.

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Assignments** (Asignaciones), seleccione **Users or workload identities** (Identidades de usuario o de carga de trabajo).
   1. En **What does this policy apply to?** (¿A qué se aplica esta directiva?), seleccione **Identidades de carga de trabajo (versión preliminar)** .
   1. En **Incluir**, elija **Select service principals** (Seleccionar entidades de servicio) y seleccione las entidades de servicio adecuadas de la lista.
1. En **Aplicaciones o acciones en la nube**, seleccione **Todas las aplicaciones en la nube**. La directiva solo se aplicará cuando una entidad de servicio solicite un token.
1. En **Condiciones** > **Ubicaciones**, incluya **Cualquier ubicación** y excluya **Ubicaciones seleccionadas** donde quiera permitir el acceso.
1. En **Conceder**, **Bloquear acceso** es la única opción disponible. El acceso se bloquea cuando se realiza una solicitud de token desde fuera del intervalo permitido.
1. La directiva se puede guardar en modo de **solo informe**, lo que permite a los administradores estimar los efectos, o bien la directiva se aplica **activando** la directiva.
1. Seleccione **Crear** para completar la directiva.

## <a name="roll-back"></a>Reversión

Si desea revertir esta característica, puede eliminar o deshabilitar las directivas creadas.

## <a name="sign-in-logs"></a>Registros de inicio de sesión

Los registros de inicio de sesión se usan para revisar cómo se aplica la directiva para las entidades de servicio o los efectos previsibles de la directiva si se usa el modo de solo informe.

1. Vaya a **Azure Active Directory** > **Registros de inicio de sesión** > **Inicios de sesión de entidad de servicio**.
1. Seleccione una entrada de registro y elija la pestaña **Acceso condicional** para ver la información de evaluación.

Motivo del error cuando el acceso condicional bloquea la entidad de servicio: "Se ha bloqueado el acceso debido a las directivas de acceso condicional". 

## <a name="reference"></a>Referencia

### <a name="finding-the-objectid"></a>Búsqueda de objectID

Puede obtener el elemento objectID de la entidad de servicio en las aplicaciones empresariales de Azure AD. No se puede usar el identificador de objeto de los registros de aplicaciones de Azure AD. Este identificador es el identificador de objeto del registro de la aplicación, no de la entidad de servicio.

1. Vaya a **Azure Portal** > **Azure Active Directory** > **Aplicaciones empresariales** y busque la aplicación que registró.
1. En la pestaña **Información general**, copie el **identificador de objeto** de la aplicación. Este identificador es el único de la entidad de servicio que usa la directiva de acceso condicional para buscar la aplicación que realiza la llamada.

### <a name="microsoft-graph"></a>Microsoft Graph

JSON de ejemplo para la configuración mediante el punto de conexión beta de Microsoft Graph.

```json
{
  "displayName": "Name",
  "state": "enabled OR disabled OR enabledForReportingButNotEnforced",
  "conditions": {
    "applications": {
      "includeApplications": [
        "All"
      ]
    },
    "clientApplications": {
      "includeServicePrincipals": [
        "[Service principal Object ID] OR ServicePrincipalsInMyTenant"
      ],
      "excludeServicePrincipals": [
        "[Service principal Object ID]"
      ],
    },
    "locations": {
      "includeLocations": [
        "All"
      ],
      "excludeLocations": [
        "[Named location ID] OR AllTrusted"
      ]
    }
  },
  "grantControls": {
    "operator": "and",
    "builtInControls": [
      "block"
    ]
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

- [Uso la condición de ubicación en una directiva de acceso condicional](location-condition.md)
- [Acceso condicional: Acceso mediante programación](howto-conditional-access-apis.md)
- [¿Qué es el modo de solo informe del acceso condicional?](concept-conditional-access-report-only.md)
 