---
title: Administración de dispositivos en Azure AD mediante Azure Portal
description: En este artículo se describe cómo usar Azure Portal para administrar identidades de dispositivo y supervisar la información de eventos relacionada.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/14/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9cdb24ac332530a8294cd6a39b5fe58ab2727ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049945"
---
# <a name="manage-device-identities-by-using-the-azure-portal"></a>Administración de identidades de dispositivo mediante Azure Portal

Azure Active Directory (Azure AD) proporciona un lugar central para administrar identidades de dispositivo y supervisar la información de eventos relacionada.

[![Captura de pantalla que muestra la información general de los dispositivos en Azure Portal.](./media/device-management-azure-portal/devices-azure-portal.png)](./media/device-management-azure-portal/devices-azure-portal.png#lightbox)

Para acceder a la información general de los dispositivos, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Vaya a **Azure Active Directory** > **Dispositivos**.

En la información general de los dispositivos, puede ver el número total de dispositivos, dispositivos obsoletos, dispositivos no conformes y dispositivos no administrados. También encontrará vínculos a Intune, al acceso condicional, a las claves de BitLocker y a la supervisión básica. 

Los recuentos de dispositivos de la página de información general no se actualizan en tiempo real. Los cambios deben reflejarse cada pocas horas.

Desde allí, puede ir a **Todos los dispositivos** para:

- Identificar los dispositivos, incluidos:
   - Dispositivos que se han unido o registrado en Azure AD.
   - Dispositivos implementados mediante [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot).
   - Impresoras que usan [Impresión universal](/universal-print/fundamentals/universal-print-getting-started).
- Realizar tareas de administración de identidades de dispositivo, como habilitar, deshabilitar, eliminar y administrar.
   - Las opciones de administración para [impresoras](/universal-print/fundamentals/) y [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) son limitadas en Azure AD. Estos dispositivos deben administrarse desde sus respectivas interfaces de administración.
- Configurar los valores de la identidad del dispositivo.
- Habilitar o deshabilitar Enterprise State Roaming.
- Revisar los registros de auditoría relacionados con los dispositivos.
- Descargar dispositivos (versión preliminar).

[![Captura de pantalla que muestra la vista Todos los dispositivos en Azure Portal.](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

> [!TIP]
> - Los dispositivos Windows 10 híbridos unidos a Azure AD no tienen un propietario. Si busca un dispositivo por propietario y no lo encuentra, busque por el identificador de dispositivo.
>
> - Si ve un dispositivo **unido a Azure AD híbrido** con un estado **Pendiente** en la columna **Registrado**, significa que el dispositivo se ha sincronizado desde Azure AD Connect y está esperando a completar el registro desde el cliente. Consulte [Planeación de la implementación de la unión a Azure AD híbrido](hybrid-azuread-join-plan.md). Para más información, consulte las [preguntas frecuentes sobre administración de dispositivos](faq.yml).
>
> - En algunos dispositivos iOS, los nombres de dispositivos que contienen apóstrofos pueden usar caracteres diferentes similares a los apóstrofos. Por lo tanto, la búsqueda de estos dispositivos es un poco complicada. Si no ve los resultados de búsqueda correctos, asegúrese de que la cadena de búsqueda contiene caracteres de apóstrofo que coincidan.

## <a name="manage-an-intune-device"></a>Administración de un dispositivo de Intune

Si tiene derechos para administrar dispositivos en Intune, puede administrar dispositivos para los que la administración de dispositivos móviles aparezca como **Microsoft Intune**. Si el dispositivo no está inscrito con Microsoft Intune, la opción **Administrar** no estará disponible.

## <a name="enable-or-disable-an-azure-ad-device"></a>Habilitación o deshabilitación de un dispositivo de Azure AD

Hay dos maneras de habilitar o deshabilitar dispositivos:

- La barra de herramientas de la página **Todos los dispositivos**, después de seleccionar uno o más dispositivos.
- La barra de herramientas, después de profundizar en un dispositivo específico.

> [!IMPORTANT]
> - Para habilitar o deshabilitar un dispositivo, tiene que ser administrador global, un administrador de Intune o administrador de dispositivos en la nube de Azure AD. 
> - Deshabilitar un dispositivo evita que se autentique mediante Azure AD. Esto evita que acceda a los recursos de Azure AD que están protegidos por el acceso condicional basado en dispositivos y que use las credenciales de Windows Hello para empresas.
> - Al deshabilitar un dispositivo, se revocan el token de actualización principal (PRT) y los tokens de actualización del dispositivo.
> - Las impresoras no se pueden habilitar ni deshabilitar en Azure AD.

## <a name="delete-an-azure-ad-device"></a>Eliminar un dispositivo de Azure AD

Hay dos maneras de eliminar un dispositivo:

- La barra de herramientas de la página **Todos los dispositivos**, después de seleccionar uno o más dispositivos.
- La barra de herramientas, después de profundizar en un dispositivo específico.

> [!IMPORTANT]
> - Debe ser administrador de dispositivos en la nube, administrador de Intune o administrador global en Azure AD para eliminar un dispositivo.
> - Las impresoras o los dispositivos Windows Autopilot no se pueden eliminar en Azure AD.
> - La eliminación de un dispositivo:
>    - Impide que acceda a los recursos de Azure AD.
>    - Quita todos los detalles asociados al dispositivo. Por ejemplo, las claves de BitLocker de los dispositivos Windows.  
>    - Es una actividad irrecuperable. No se recomienda a menos que sea necesario.

Si otra entidad de administración administra un dispositivo, como Microsoft Intune, asegúrese de que se borra o se retira antes de eliminarlo. Consulte [cómo administrar dispositivos obsoletos](manage-stale-devices.md) antes de eliminar un dispositivo.

## <a name="view-or-copy-a-device-id"></a>Visualización o copia de un identificador de dispositivo

Puede usar un identificador de dispositivo para comprobar los detalles del identificador de dispositivo en el dispositivo o para solucionar problemas a través de PowerShell. Para acceder a la opción de copia, seleccione el dispositivo.

![Captura de pantalla que muestra un identificador de dispositivo y el botón Copiar.](./media/device-management-azure-portal/35.png)
  
## <a name="view-or-copy-bitlocker-keys"></a>Ver o copiar las claves de BitLocker

Puede ver y copiar claves de BitLocker para permitir a los usuarios recuperar unidades cifradas. Estas claves solo están disponibles para dispositivos Windows cifrados y almacenan sus claves en Azure AD. Puede encontrar estas claves al ver los detalles de un dispositivo mediante la selección de **Mostrar clave de recuperación**. Al seleccionar **Mostrar clave de recuperación**, se generará un registro de auditoría que puede encontrar en la categoría `KeyManagement`.

![Captura de pantalla que muestra cómo ver las claves de BitLocker.](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Para ver o copiar las claves de BitLocker, debe ser el propietario del dispositivo o bien tener al menos uno de estos roles:

- Administrador de dispositivos en la nube
- Administrador global
- Administrador del departamento de soporte técnico
- Administrador de servicios de Intune
- Administrador de seguridad
- Lector de seguridad

## <a name="device-list-filtering-preview"></a>Filtrado de la lista de dispositivos (versión preliminar)

Anteriormente, solo podía filtrar la lista de dispositivos por actividad y estado habilitado. En esta versión preliminar, puede filtrar la lista de dispositivos por estos atributos de dispositivo:

- Estado habilitado
- Estado de cumplimiento
- Tipo de combinación (unido a Azure AD, unido a Azure AD híbrido, registrado en Azure AD)
- Marca de tiempo de actividad
- SO
- Tipo de dispositivo (impresora, máquina virtual segura, dispositivo compartido, dispositivo registrado)

Para habilitar la funcionalidad de filtrado de versión preliminar en la vista **Todos los dispositivos**:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Vaya a **Azure Active Directory** > **Dispositivos**.
1. Seleccione el banner que dice **Pruebe las mejoras de la nueva experiencia de filtrado de dispositivos. Haga clic para habilitar la versión preliminar.**

   ![Habilitación de la funcionalidad de versión preliminar de filtrado](./media/device-management-azure-portal/device-filter-preview-enable.png)

Ahora puede agregar filtros a la vista **Todos los dispositivos**.

## <a name="download-devices-preview"></a>Descargar dispositivos (versión preliminar)

Los administradores de dispositivos en la nube, los administradores de Intune y los administradores globales pueden usar la opción **Descargar dispositivos (versión preliminar)** para exportar un archivo CSV que muestre los dispositivos. Puede aplicar filtros para determinar qué dispositivos se mostrarán. Si no aplica ningún filtro, se mostrarán todos los dispositivos. Una tarea de exportación puede ejecutarse durante una hora, en función de las selecciones.

La lista exportada incluye estos atributos de identidad del dispositivo:

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>Configuración de dispositivo

Si desea administrar las identidades de los dispositivos mediante Azure Portal, los dispositivos deben estar [registrados o unidos](overview.md) a Azure AD. Como administrador, puede controlar el proceso de registro y unión de dispositivos mediante la configuración de los siguientes valores del dispositivo.

Debe tener asignado uno de los roles siguientes para ver o administrar la configuración del dispositivo en Azure Portal:

- Administrador global
- Administrador de dispositivos en la nube
- Lector global
- Lector del directorio

![Captura de pantalla que muestra la configuración de dispositivos en relación con Azure AD.](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Los usuarios pueden unir sus dispositivos a Azure AD**: esta opción le permite seleccionar qué usuarios podrán registrar sus dispositivos como dispositivos de unión a Azure AD. El valor predeterminado es **Todos**.

   > [!NOTE]
   > La opción **Los usuarios pueden inscribir dispositivos en Azure AD**  solo puede utilizarse en Unión a Azure AD en Windows 10. Esta configuración no se aplica a los dispositivos unidos a Azure AD híbrido, a las [máquinas virtuales unidas a Azure AD en Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) ni a dispositivos unidos a Azure AD mediante el [modo de autoimplementación de Windows Autopilot](/mem/autopilot/self-deploying), ya que estos métodos funcionan en contextos sin usuario.

- **Administradores locales adicionales en dispositivos unidos a Azure AD**: esta opción le permite seleccionar a qué usuarios se conceden derechos de administrador local en un dispositivo. Estos usuarios se agregan al rol Administradores de dispositivos en Azure AD. De forma predeterminada, a los administradores globales de Azure AD y a los propietarios de dispositivos se les conceden derechos de administrador local. Esta opción es una funcionalidad de edición Premium disponible en productos como Azure AD Premium y Enterprise Mobility + Security.
- **Los usuarios pueden registrar sus dispositivos con Azure AD**: esta opción se debe configurar para que los usuarios puedan registrar los dispositivos con Windows 10 personal, iOS, Android y macOS se puedan registrar en Azure AD. Si selecciona **Ninguno**, los dispositivos no podrán registrarse con Azure AD. La inscripción con Microsoft Intune o Administración de dispositivos móviles para Microsoft 365 exige registrarse. Si ha configurado alguno de estos servicios, se selecciona **TODOS** y **NINGUNO** no está disponible.
- **Requerir Multi-Factor Authentication para registrar o unir dispositivos con Azure AD**: esta opción le permite especificar si se les exige a los usuarios que proporcionen otro factor de autenticación para unir sus dispositivos a Azure AD o registrarlos ahí. El valor predeterminado es **No**. Se recomienda requerir la autenticación multifactor cuando un dispositivo se registre o una. Antes de habilitar la autenticación multifactor para este servicio, debe asegurarse de que la autenticación multifactor está configurada para los usuarios que registran sus dispositivos. Para obtener más información sobre los servicios de Azure AD Multi-Factor Authentication, consulte [Introducción a Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). 

   > [!NOTE]
   > La opción **Requerir Multi-Factor Authentication para registrar o unir dispositivos con Azure AD** se aplica a los dispositivos que están unidos a Azure AD (con algunas excepciones) o registrados en Azure AD. Esta configuración no se aplica a los dispositivos unidos a Azure AD híbrido, a las [máquinas virtuales unidas a Azure AD en Azure](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) ni a dispositivos unidos a Azure AD mediante el [modo de autoimplementación de Windows Autopilot](/mem/autopilot/self-deploying).

   > [!IMPORTANT]
   > - Se recomienda usar la acción del [usuario Registrar o unir dispositivos](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) en el acceso condicional para exigir la autenticación multifactor para la unión o registro de un dispositivo. 
   > - Debe configurar esta opción en **No** si usa la directiva de acceso condicional para exigir la autenticación multifactor. 

- **Número máximo de dispositivos**: esta opción permite seleccionar el número máximo de dispositivos unidos a Azure AD o registrados en Azure AD que puede tener un usuario en Azure AD. Si los usuarios alcanzan este límite, no pueden agregar más dispositivos hasta que se quiten uno o varios de los dispositivos existentes. El valor predeterminado es **50**. Puede aumentar el valor hasta 100. Si escribe un valor por encima de 100, Azure AD lo establecerá en 100. También puede usar un valor **ilimitado** para no aplicar otros límites aparte de los de cuota existentes.

   > [!NOTE]
   > La configuración del **número máximo de dispositivos** se aplica a los dispositivos que están unidos a Azure AD o registrados en Azure AD. Esta opción no se aplica a los dispositivos unidos a Azure AD híbridos.

- **Enterprise State Roaming**: para obtener información sobre esta configuración, consulte [el artículo de información general](enterprise-state-roaming-overview.md).

## <a name="audit-logs"></a>Registros de auditoría

Las actividades de un dispositivo están visibles en los registros de actividad. Estos registros contienen las actividades desencadenadas por el servicio de registro de dispositivos y los usuarios:

- Creación de dispositivos e incorporación de propietarios o usuarios al dispositivo
- Cambios en la configuración de un dispositivo
- Operaciones de dispositivo como eliminar o actualizar un dispositivo

El punto de entrada a los datos de auditoría está en **Registros de auditoría**, que se encuentra en la sección **Actividad** de la página **Dispositivos**.

El registro de auditoría tiene una vista de lista predeterminada que muestra:

- La fecha y hora de la repetición.
- Los destinos.
- El iniciador o actor de una actividad.
- La actividad.

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Captura de pantalla que muestra una tabla en la sección de actividad de la página de dispositivos en la que se enumeran la fecha, el destino, el actor y la actividad de cuatro registros de auditoría." border="false":::

Puede personalizar la vista de lista seleccionando **Columnas** en la barra de herramientas:

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Captura de pantalla en la que se muestra la barra de herramientas de la página de dispositivos." border="false":::

Para reducir los datos notificados a un nivel que se adapte a sus necesidades, puede filtrarlo mediante estos campos:

- **Categoría**
- **Tipo de recurso de actividad**
- **Actividad**
- **Date Range** (Intervalo de fechas)
- **Target**
- **Iniciado por (actor)**

También puede buscar entradas concretas.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Captura de pantalla que muestra los controles de filtrado de datos de auditoría." border="false":::

## <a name="next-steps"></a>Pasos siguientes

- [Procedimiento para administrar los dispositivos obsoletos en Azure AD](manage-stale-devices.md)
- [Solución de problemas de estado de dispositivo pendiente](/troubleshoot/azure/active-directory/pending-devices)
