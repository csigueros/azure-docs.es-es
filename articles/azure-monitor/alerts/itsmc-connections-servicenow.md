---
title: Conexión de ServiceNow al Conector de Administración de servicios de TI
description: Aprenda a conectar ServiceNow al Conector de Administración de servicios de TI (ITSMC) en Azure Monitor para supervisar y administrar de forma centralizada los elementos de trabajo de ITSM.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: e3d696580acd995a2396d56c404e2f904611ccac
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430143"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Conexión de ServiceNow al Conector de Administración de servicios de TI

En este artículo se muestra cómo configurar la conexión entre la instancia de ServiceNow y el Conector de Administración de servicios de TI (ITSMC) en Log Analytics para administrar de forma centralizada los elementos de trabajo de Administración de servicios de TI (ITSM).

## <a name="prerequisites"></a>Prerrequisitos
Asegúrese de que se cumplen los siguientes requisitos previos para la conexión.

### <a name="itsmc-installation"></a>Instalación de ITSMC

Para obtener información acerca de cómo instalar ITSMC, consulte [Adición del Conector de Administración de servicios de TI](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> ITSMC admite solo la oferta de software como servicio (SaaS) oficial de ServiceNow. No se admiten implementaciones privadas de ServiceNow.

### <a name="oauth-setup"></a>Configuración de OAuth

Entre las versiones admitidas de ServiceNow se incluyen Quebec, París, Orlando, Nueva York, Madrid, Londres, Kingston, Yakarta, Estambul, Helsinki y Ginebra.

Los administradores de ServiceNow deben generar un id. de cliente y un secreto de cliente para su instancia de ServiceNow. Consulte la siguiente información según sea necesario:

- [Configuración de OAuth para Quebec](https://docs.servicenow.com/bundle/quebec-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configuración de OAuth para París](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configuración de OAuth para Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [Configuración de OAuth para Londres](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)

Como parte de la configuración de OAuth, se recomienda:

1. [Cree un punto de conexión para que los clientes tengan acceso a la instancia](https://docs.servicenow.com/bundle/rome-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Actualice la duración del token de actualización:

   1. En el panel **ServiceNow**, busque **OAuth del sistema** y, a continuación, seleccione **Registro de aplicaciones**. 
   1. Seleccione el nombre de OAuth que se definió y cambie la **duración del token de actualización** a **7 776 000 segundos** (90 días). 
   1. Selecciona **Actualización**. 

1. Establezca un procedimiento interno para asegurarse de que la conexión permanezca activa. Un par de días antes de la expiración esperada de la duración del token de actualización, realice las operaciones siguientes:

   1. [Complete un proceso de sincronización manual para la configuración del conector de ITSM](./itsmc-resync-servicenow.md).

   1. Revoque al antiguo token de actualización. No se recomienda mantener las claves antiguas por motivos de seguridad. 
   
      1. En el panel **ServiceNow**, busque **OAuth del sistema** y, a continuación, seleccione **Administrar tokens**. 
      
      1. Seleccione el token antiguo de la lista según el nombre de OAuth y la fecha de expiración.

         ![Captura de pantalla que muestra una lista de tokens de OAuth.](media/itsmc-connections-servicenow/snow-system-oauth.png)
      1. Seleccione **Revocar acceso** > **Revocar**.

## <a name="install-the-user-app-and-create-the-user-role"></a>Instalación de la aplicación de usuario y creación del rol de usuario

Use el procedimiento siguiente para instalar la aplicación de usuario de ServiceNow y crear el rol de usuario de integración para ella. Utilizará estas credenciales para realizar la conexión de ServiceNow en Azure.

> [!NOTE]
> ITSMC solo es compatible con la aplicación de usuario oficial para la integración de Microsoft Log Analytics que se descarga del almacén de ServiceNow. ITSMC no admite la ingesta de código en el lado de ServiceNow ni la aplicación que no forma parte de la solución oficial de ServiceNow. 

1. Visite la [tienda de ServiceNow](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) e instale la **aplicación de usuario para la integración de OMS de Microsoft y ServiceNow** en la instancia de ServiceNow.
   
   >[!NOTE]
   >Como parte de la transición continuada de Microsoft Operations Management Suite (OMS) a Azure Monitor, OMS ahora se denomina Log Analytics.     
2. Después de la instalación, vaya a la barra de navegación izquierda de la instancia de ServiceNow, y busque y seleccione el **integrador de OMS de Microsoft**.  
3. Seleccione **Lista de comprobación de instalación**.

   El estado se muestra como **Sin completar** porque aún no se creó el rol de usuario.

4. En el cuadro de texto, junto a **Crear usuario de integración**, escriba el nombre de usuario para el usuario que puede conectarse a ITSMC en Azure.
5. Escriba la contraseña para este usuario y seleccione **Aceptar**.  

El usuario recién creado se muestra con los roles predeterminados asignados:

- personalize_choices
- import_transformer
- x_mioms_microsoft.User
- itil
- template_editor
- view_changer

Después de crear el usuario correctamente, el estado de **Comprobar lista de comprobación de instalación** pasa a **Completado** y se muestran los detalles del rol de usuario creado para la aplicación.

> [!NOTE]
> ITSM puede enviar incidentes a ServiceNow sin otros módulos instalados en la instancia de ServiceNow. Si usa el módulo EventManagement en su instancia de ServiceNow y quiere crear eventos o alertas en ServiceNow mediante el conector, agregue los siguientes roles para el usuario de integración:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Crear una conexión
Use el procedimiento siguiente para crear una nueva conexión a ServiceNow.

> [!NOTE]
> Las alertas que se envían desde Azure Monitor pueden crear uno de los siguientes elementos en ServiceNow: eventos, incidentes o alertas. 

1. En Azure Portal, vaya a **Todos los recursos** y busque **ServiceDesk(YourWorkspaceName)** .

2. En **Orígenes de datos del área de trabajo**, seleccione **Conexiones de ITSM**.

   ![Captura de pantalla que muestra la selección de un origen de datos.](media/itsmc-overview/add-new-itsm-connection.png)

3. En la parte superior del panel derecho, seleccione **Agregar**.

4. Proporcione la información tal como se describe en la tabla siguiente y, a continuación, seleccione **Aceptar**.

   | **Campo** | **Descripción** |
   | --- | --- |
   | **Nombre de la conexión**   | Escriba un nombre para la instancia de ServiceNow que quiere conectar con ITSMC. Usará este nombre más adelante en Log Analytics cuando configure los elementos de trabajo de ITSM y vea el análisis de registros. |
   | **Tipo de asociado**   | Seleccione **ServiceNow**. |
   | **Dirección URL del servidor**   | Escriba la dirección URL de la instancia de ServiceNow que quiere conectar con ITSMC. La dirección URL debe apuntar a una versión de SaaS compatible con el sufijo *.servicenow.com* (por ejemplo, `https://XXXXX.service-now.com/`).|
   | **Nombre de usuario**   | Escriba el nombre de usuario de integración que ha creado en la aplicación de ServiceNow para que admita la conexión con ITSMC.|
   | **Contraseña**   | Escriba la contraseña asociada con este nombre de usuario. **Nota**: El nombre de usuario y la contraseña se utilizan para generar únicamente tokens de autenticación. No se almacenan en ningún lugar dentro del servicio ITSMC.  |
   | **Id. de cliente**   | Escriba el id. de cliente que quiere utilizar para la autenticación de OAuth2 y que ha generado anteriormente. Para obtener más información sobre la generación de un id. de cliente y un secreto, consulte [Configuración de OAuth](https://old.wiki/index.php/OAuth_Setup). |
   | **Secreto de cliente**   | Escriba el secreto de cliente generado para este identificador.   |
   | **Ámbito de sincronización de datos (en días)** | Escriba el número de días pasados de los que quiere los datos. El límite es de 120 días. |
   | **Elementos de trabajo para sincronizar**   | Seleccione los elementos de trabajo de ServiceNow que quiere sincronizar con Azure Log Analytics mediante ITSMC. Los valores seleccionados se importan en Log Analytics. Las opciones son incidentes y solicitudes de cambio.|
   | **Crear elemento de configuración en el producto ITSM** | Seleccione esta opción si desea crear los elementos de configuración en el producto ITSM. Cuando se selecciona, ITSMC crea elementos de configuración (si no existen) en el sistema ITSM compatible. De forma predeterminada, está deshabilitada. |

![Captura de pantalla de cuadros y opciones para agregar una conexión de ServiceNow.](media/itsmc-connections-servicenow/itsm-connection-servicenow-connection-latest.png)

Cuando se ha conectado y sincronizado correctamente:

- Los elementos de trabajo seleccionados en la instancia de ServiceNow se importan en Log Analytics. Puede ver el resumen de estos elementos de trabajo en el icono de **Conector de Administración de servicios de TI**.

- Puede crear incidentes a partir de alertas o entradas de registros de Log Analytics o alertas de Azure en esta instancia de ServiceNow.

> [!NOTE]
> ServiceNow tiene un límite de frecuencia para las solicitudes por hora. Para configurar dicho límite, defina el valor de **Limitación de velocidad de la API REST de entrada** en la instancia de ServiceNow.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción al Conector de Administración de servicios de TI](itsmc-overview.md)
* [Creación de elementos de trabajo de ITSM a partir de alertas de Azure](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Solución de problemas del Conector de Administración de servicios de TI](./itsmc-resync-servicenow.md)
