---
title: 'Filtro para dispositivos como condición en la directiva de acceso condicional: Azure Active Directory'
description: Uso del filtro para dispositivos en el acceso condicional para mejorar la posición de seguridad
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7dc7b4630d26c0f1b78df5a29a3210fd68f3d734
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012669"
---
# <a name="conditional-access-filter-for-devices"></a>Acceso condicional: filtro para dispositivos

Al crear directivas de acceso condicional, los administradores han solicitado la capacidad de dirigirse a dispositivos específicos en su entorno o de excluirlos. La condición de filtro para dispositivos proporciona esta funcionalidad a los administradores. Ahora puede dirigirse a dispositivos específicos mediante [operadores admitidos y propiedades para filtros del dispositivo](#supported-operators-and-device-properties-for-filters) y otras condiciones de asignación disponibles en las directivas de acceso condicional.

:::image type="content" source="media/concept-condition-filters-for-devices/create-filter-for-devices-condition.png" alt-text="Creación de un filtro para dispositivo en las condiciones de directiva de acceso condicional":::


## <a name="common-scenarios"></a>Escenarios frecuentes

Hay varios escenarios que las organizaciones ahora pueden habilitar mediante la condición de filtro para dispositivos. A continuación se muestran algunos de los escenarios principales con ejemplos de uso de esta nueva condición.

- Restrinja el acceso a recursos con privilegios, como Microsoft Azure Management, a los usuarios con privilegios, que acceden desde [estaciones de trabajo de administración con privilegios o seguras](/security/compass/privileged-access-devices). En este escenario, las organizaciones crearían dos directivas de acceso condicional:
   - Directiva 1: todos los usuarios con el rol de directorio Administrador global con acceso a la aplicación en la nube Microsoft Azure Management y, en Controles de acceso, Conceder acceso, pero que requieren autenticación multifactor y que el dispositivo esté marcado como compatible.
   - Directiva 2: todos los usuarios con el rol de directorio Administrador global y acceso a la aplicación en la nube Microsoft Azure Management, excepto los filtros para dispositivos que usan la expresión de regla device.extensionAttribute1 equals SAW y, en Controles de acceso, Bloquear.
- Bloquee el acceso a los recursos de la organización desde dispositivos que ejecuten una versión de sistema operativo no compatible, como Windows 7. En este escenario, las organizaciones crearían las dos directivas de acceso condicional siguientes:
   - Directiva 1: todos los usuarios con acceso a todas las aplicaciones en la nube y, en Controles de acceso, Conceder acceso, pero que requieren que el dispositivo esté marcado como compatible o que esté unido a Azure AD híbrido.
   - Directiva 2: todos los usuarios con acceso a todas las aplicaciones en la nube, incluido un filtro para dispositivos que usan la expresión de regla device.operatingSystem equals Windows y device.operatingSystemVersion startsWith "6.1" y, en Controles de acceso, Bloquear.
- No se requiere la autenticación multifactor para cuentas específicas, como las cuentas de servicio, cuando se usan en dispositivos específicos, como teléfonos con Teams o dispositivos Surface Hub. En este escenario, las organizaciones crearían las dos directivas de acceso condicional siguientes:
   - Directiva 1: todos los usuarios excepto las cuentas de servicio, con acceso a todas las aplicaciones en la nube y, en Controles de acceso, Conceder acceso, pero que requieren autenticación multifactor.
   - Directiva 2: seleccione usuarios y grupos e incluya un grupo que contenga solo cuentas de servicio, con acceso a todas las aplicaciones en la nube, excepto un filtro de los dispositivos que usan la expresión de regla device.extensionAttribute2 equals TeamsPhoneDevice y, en Controles de acceso, Bloquear.

## <a name="create-a-conditional-access-policy"></a>Creación de una directiva de acceso condicional

El filtro para dispositivos es una opción al crear una directiva de acceso condicional en Azure Portal o con Microsoft Graph API.

> [!IMPORTANT]
> El estado del dispositivo y el filtro para dispositivos no se pueden usar juntos en la directiva de acceso condicional.

Los pasos siguientes le ayudarán a crear dos directivas de acceso condicional para admitir el primer escenario de [Escenarios comunes](#common-scenarios). 

Directiva 1: todos los usuarios con el rol de directorio Administrador global con acceso a la aplicación en la nube Microsoft Azure Management y, en Controles de acceso, Conceder acceso, pero que requieren autenticación multifactor y que el dispositivo esté marcado como compatible.

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Roles del directorio** y elija **Administrador global**.
   
      > [!WARNING]
      > Las directivas de acceso condicional admiten los roles integrados. Las directivas de acceso condicional no se aplican a otros tipos de roles, incluidos los roles con [ámbito de unidad administrativa](../roles/admin-units-assign-roles.md) o [personalizados](../roles/custom-create.md).

   1. En **Excluir**, seleccione **Usuarios y grupos** y, luego, elija las cuentas de acceso de emergencia de la organización. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, elija **Seleccionar aplicaciones** y **Microsoft Azure Management**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**, **Requerir autenticación multifactor** y **Requerir que el dispositivo se marque como compatible** y, a continuación, elija **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.
1. Seleccione **Crear** para crear la directiva.

Directiva 2: todos los usuarios con el rol de directorio Administrador global y acceso a la aplicación en la nube Microsoft Azure Management, excepto los filtros para dispositivos que usan la expresión de regla device.extensionAttribute1 equals SAW y, en Controles de acceso, Bloquear.

1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Roles del directorio** y elija **Administrador global**.
   
      > [!WARNING]
      > Las directivas de acceso condicional admiten los roles integrados. Las directivas de acceso condicional no se aplican a otros tipos de roles, incluidos los roles con [ámbito de unidad administrativa](../roles/admin-units-assign-roles.md) o [personalizados](../roles/custom-create.md).

   1. En **Excluir**, seleccione **Usuarios y grupos** y, luego, elija las cuentas de acceso de emergencia de la organización. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, elija **Seleccionar aplicaciones** y **Microsoft Azure Management**.
1. En **Condiciones**, **Filtro para dispositivos**.
   1. Establezca **Configurar** en **Sí**.
   1. Establezca **Devices matching the rule** (Dispositivos que coincidan con la regla) en **Exclude filtered devices from policy** (Excluir dispositivos filtrados de la directiva).
   1. Establezca la propiedad en `ExtensionAttribute1`, el operador en `Equals` y el valor en `SAW`.
   1. Seleccione **Listo**.
1. En **Controles de acceso** > **Conceder**, seleccione **Bloquear acceso** y, después, **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.
1. Seleccione **Crear** para crear la directiva.

### <a name="filter-for-devices-graph-api"></a>Graph API del filtro para dispositivos

La API de filtros para dispositivos está disponible en el punto de conexión de Microsoft Graph v1.0 y es accesible mediante https://graph.microsoft.com/v1.0/identity/conditionalaccess/policies/. Puede configurar un filtro para dispositivos al crear una nueva directiva de acceso condicional, o puede actualizar una directiva existente para configurar la condición del filtro para dispositivos. Para actualizar una directiva existente, puede realizar una llamada de revisión en el punto de conexión de Microsoft Graph v1.0 mencionado anteriormente. Para ello, anexe el identificador de directiva de una directiva existente y ejecute el siguiente cuerpo de solicitud. En el ejemplo siguiente se muestra cómo configurar una condición de filtro para dispositivos, excepto dispositivos que no estén marcados como SAW. La sintaxis de la regla puede constar de más de una expresión única. Para más información, consulte [Reglas de pertenencia dinámica a grupos de Azure Active Directory](../enterprise-users/groups-dynamic-membership.md). 

```json
{
    "conditions": {
        "devices": {
            "deviceFilter": {
                "mode": "exclude",
                "rule": "device.extensionAttribute1 -ne \"SAW\""
            }
        }
    }
}
```

## <a name="supported-operators-and-device-properties-for-filters"></a>Operadores admitidos y propiedades de dispositivo para filtros

Los siguientes atributos de dispositivo se pueden usar con la condición de filtro para dispositivos en el acceso condicional.

| Atributos de dispositivo admitidos | Operadores admitidos | Valores admitidos | Ejemplo |
| --- | --- | --- | --- |
| deviceId | Equals, NotEquals, In, NotIn | Un deviceId válido que sea un GUID | (device.deviceid -eq “498c4de7-1aee-4ded-8d5d-000000000000”) |
| DisplayName | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Cualquier cadena | (device.displayName -contains “ABC”) |
| deviceOwnership | Equals, NotEquals | Los valores admitidos son "Personal" para Bring Your Own Device y "Company" para dispositivos propiedad de la empresa.  | (device.deviceOwnership -eq “Company”) |
| isCompliant | Equals, NotEquals | Los valores admitidos son "True" para dispositivos compatibles y "False" para dispositivos no compatibles.  | (device.isCompliant -eq “True”) |
| fabricante | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Cualquier cadena | (device.manufacturer -startsWith “Microsoft”) |
| mdmAppId | Equals, NotEquals, In, NotIn | Un identificador de aplicación MDM válido | (device.mdmAppId -in [“0000000a-0000-0000-c000-000000000000”] |
| model | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Cualquier cadena | (device.model -notContains “Surface”) |
| operatingSystem | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Un sistema operativo válido (como Windows, iOS o Android) | (device.operatingSystem -eq “Windows”) |
| operatingSystemVersion | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Una versión de sistema operativo válida (como 6.1 para Windows 7, 6.2 para Windows 8 o 10.0 para Windows 10) | (device.operatingSystemVersion -in [“10.0.18363”, “10.0.19041”, “10.0.19042”]) |
| physicalIds | Contains, NotContains | Por ejemplo, todos los dispositivos Windows Autopilot almacenan ZTDId (un valor único asignado a todos los dispositivos Windows Autopilot importados) en la propiedad physicalIds del dispositivo. | (device.devicePhysicalIDs -contains "[ZTDId]:value") |
| profileType | Equals, NotEquals | Un tipo de perfil válido establecido para un dispositivo. Los valores admitidos son: RegisteredDevice (valor predeterminado), SecureVM (se usa para máquinas virtuales Windows en Azure habilitadas con el inicio de sesión de Azure AD), Printer (se usa para impresoras), Shared (se usa para dispositivos compartidos) y IoT (se usa para dispositivos IoT). | (device.profileType -notIn [“Printer”, “Shared”, “IoT”] |
| systemLabels | Contains, NotContains | Lista de etiquetas que el sistema aplica al dispositivo. Algunos de los valores admitidos son: AzureResource (se usa para máquinas virtuales Windows en Azure habilitadas con el inicio de sesión de Azure AD), M365Managed (se usa para dispositivos administrados mediante Escritorio administrado de Microsoft) y MultiUser (se usa para dispositivos compartidos). | (device.systemLabels -contains "M365Managed") |
| trustType | Equals, NotEquals | Un estado registrado válido para dispositivos. Los valores admitidos son: AzureAD (se usa para dispositivos unidos a Azure AD), ServerAD (se usa para dispositivos unidos a Azure AD híbrido), Workplace (se usa para dispositivos registrados en Azure AD). | (device.trustType -notIn ‘ServerAD, Workplace’) |
| extensionAttribute1-15 | Equals, NotEquals, StartsWith, NotStartsWith, EndsWith, NotEndsWith, Contains, NotContains, In, NotIn | Los clientes pueden usar los atributos extensionAttributes1-15 para objetos de dispositivo. Los clientes pueden actualizar cualquiera de los atributos de extensionAttributes1 a 15 con valores personalizados y usarlos en la condición de filtro para dispositivos en el acceso condicional. Se puede usar cualquier valor de cadena. | (device.extensionAttribute1 -eq ‘SAW’) |

## <a name="policy-behavior-with-filter-for-devices"></a>Comportamiento de la directiva con el filtro para dispositivos

La condición de filtro para dispositivos del acceso condicional evalúa la directiva en función de los atributos de dispositivo de un dispositivo registrado en Azure AD y, por tanto, es importante comprender en qué circunstancias se aplica o no la directiva. En la tabla siguiente se muestra el comportamiento cuando se configura la condición de filtro para dispositivos. 

| Condición de filtro para dispositivos | Estado de registro del dispositivo | Filtro de dispositivo aplicado 
| --- | --- | --- |
| Modo de inclusión/exclusión con operadores positivos (Equals, StartsWith, EndsWith, Contains, In) y el uso de cualquier atributo | Dispositivo no registrado | No |
| Modo de inclusión/exclusión con operadores positivos (Equals, StartsWith, EndsWith, Contains, In) y uso de atributos, excluidos extensionAttributes1-15 | Dispositivo registrado | Sí, si se cumplen los criterios. |
| Modo de inclusión/exclusión con operadores positivos (Equals, StartsWith, EndsWith, Contains, In) y el uso de atributos, incluidos extensionAttributes1-15 | Dispositivo registrado administrado por Intune | Sí, si se cumplen los criterios. |
| Modo de inclusión/exclusión con operadores positivos (Equals, StartsWith, EndsWith, Contains, In) y el uso de atributos, incluidos extensionAttributes1-15 | Dispositivo registrado no administrado por Intune | Sí, si se cumplen los criterios y si el dispositivo es compatible o está unido a Azure AD híbrido. |
| Modo de inclusión/exclusión con operadores negativos (NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn) y el uso de atributos | Dispositivo no registrado | Yes |
| Modo de inclusión/exclusión con operadores negativos (NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn) y el uso de atributos, excluidos extensionAttributes1-15 | Dispositivo registrado | Sí, si se cumplen los criterios. |
| Modo de inclusión/exclusión con operadores negativos (NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn) y el uso de cualquier atributo, incluidos extensionAttributes1-15 | Dispositivo registrado administrado por Intune | Sí, si se cumplen los criterios. |
| Modo de inclusión/exclusión con operadores negativos (NotEquals, NotStartsWith, NotEndsWith, NotContains, NotIn) y el uso de cualquier atributo, incluidos extensionAttributes1-15 | Dispositivo registrado no administrado por Intune | Sí, si se cumplen los criterios y si el dispositivo es compatible o está unido a Azure AD híbrido. |

## <a name="next-steps"></a>Pasos siguientes

- [Acceso condicional: Condiciones](concept-conditional-access-conditions.md)
- [Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)
- [Protección de dispositivos como parte de la historia de acceso con privilegios](/security/compass/privileged-access-devices)
